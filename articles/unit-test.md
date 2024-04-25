关于mock
https://mp.weixin.qq.com/s/hX_RIYs-nBnqVwdq5B4rhg

测试替身理论：
    dummy
    fake
    spy: 一个真实对象，其中的方法实现都是真实的，会记录其方法调用的相关信息（如调用频次）
    stub: 对真实对象的简单实现，返回预定义的数据，是mock的子集
    mock: 模拟的对象，在测试中自定义方法行为，记录方法调用

框架：mockito
    @InjectMocks：创建实例，其余用@Mock或@Spy创建的对象将被注入到该实例中，一般是待测对象；

    @Mock：实例化一个mock对象，所有方法都被模拟空实现，返回默认值null/0，适用于普通类、接口和虚基类
            等价于Mockito.mock();方法
           可以通过Mockito.when(nbService.getName()).thenReturn("NbService");
            或Mockito.doReturn("NbService").when(nbService).getName();模拟方法行为

    @Spy：实例化真实对象，s所有方法实现都是真实的，会记录该对象的交互
            等价于Mockito.spy()
            可以通过when().then()或doReturn().when()来模拟方法行为(打桩)


定义被测对象
模拟依赖对象
注入依赖对象
模拟依赖方法
    可以根据各种条件模拟各种行为：调用原方法，返回固定值，执行lambda表达式，啥也不干等
验证依赖方法调用行为
    验证方法调用的入参:
        eg.验证testDAO.re()方法是否被调用，且入参为"你好"
        Mockito.verify(testDAO).re("你好");
    方法调用次数:
        eg.testDAO.re("你好")被调用了两次
        Mockito.verify(testDAO, Mockito.times(2)).re("你好");
    验证方法调用并捕获入参:
        eg.捕获入参
        ArgumentCaptor<String> captor = ArgumentCaptor.forClass(String.class);或者@Captor
        Mockito.verify(testDAO).re(captor.capture());
        String value = captor.getValue();
验证数据
    断言 junit4:Assert;junit5:Assertion
异常处理
验证依赖对象       
    模拟对象方法是否被调用
    Mockito.verfyNoInteractions();
    清除方法调用标记
    Mockito.clearInvocations();


junit
junit4 springboot2.2以前内置
junit5 sb2.2及之后

junit4源码：
入口：
public static void main(String[] args) {
    JUnitCore jUnitCore = new JUnitCore();
    Request request = Request.aClass(MainTest.class);
    Result result = jUnitCore.run(request.getRunner());
}


JunitCore:门面

Request：对测试类的抽象封装
an abstract description of tests to be run. 
The flow when JUnit runs tests is that 
a Request specifies some tests to be run -> 
a Runner is created for each class implied by the Request -> 
the Runner returns a detailed Description which is a tree structure of the tests to be run.

public class ClassRequest extends Request {
    private final Object runnerLock = new Object();

    private final Class<?> fTestClass;
    private final boolean canUseSuiteMethod;
    private volatile Runner runner;

    public ClassRequest(Class<?> testClass, boolean canUseSuiteMethod) {
        this.fTestClass = testClass;
        this.canUseSuiteMethod = canUseSuiteMethod;
    }

    public ClassRequest(Class<?> testClass) {
        this(testClass, true);
    }

    @Override
    public Runner getRunner() {
        if (runner == null) {
            synchronized (runnerLock) {
                if (runner == null) {
                    runner = new AllDefaultPossibilitiesBuilder(canUseSuiteMethod).safeRunnerForClass(fTestClass);
                }
            }
        }
        return runner;
    }
}

// 根据传入的测试类判断用哪个Runner
public Runner safeRunnerForClass(Class<?> testClass) {
    try {
        return runnerForClass(testClass);
    } catch (Throwable e) {
        return new ErrorReportingRunner(testClass, e);
    }
}

public Runner runnerForClass(Class<?> testClass) throws Throwable {
    List<RunnerBuilder> builders = Arrays.asList(
            ignoredBuilder(),
            annotatedBuilder(),
            suiteMethodBuilder(),
            junit3Builder(),
            junit4Builder());

    for (RunnerBuilder each : builders) {
        Runner runner = each.safeRunnerForClass(testClass);
        if (runner != null) {
            return runner;
        }
    }
    return null;
}

Runner:执行测试用例并将重要事件通知给RunNotifier

    public void run(final RunNotifier notifier) {
        EachTestNotifier testNotifier = new EachTestNotifier(notifier,
                getDescription());
        try {
            Statement statement = classBlock(notifier);
            statement.evaluate();
        } catch (AssumptionViolatedException e) {
            testNotifier.addFailedAssumption(e);
        } catch (StoppedByUserException e) {
            throw e;
        } catch (Throwable e) {
            testNotifier.addFailure(e);
        }
    }

    // childrenInvoker里找到全部test方法，包装成Staement
    // 依次执行before,method,after
    protected Statement classBlock(final RunNotifier notifier) {
        Statement statement = childrenInvoker(notifier);
        if (!areAllChildrenIgnored()) {
            statement = withBeforeClasses(statement);
            statement = withAfterClasses(statement);
            statement = withClassRules(statement);
        }
        return statement;
    }

    // BlockJunit4ClassRunner
    protected Statement methodBlock(FrameworkMethod method) {
        Object test;
        try {
            test = new ReflectiveCallable() {
                @Override
                protected Object runReflectiveCall() throws Throwable {
                    return createTest();
                }
            }.run();
        } catch (Throwable e) {
            return new Fail(e);
        }

        Statement statement = methodInvoker(method, test);
        statement = possiblyExpectingExceptions(method, test, statement);
        statement = withPotentialTimeout(method, test, statement);
        statement = withBefores(method, test, statement);
        statement = withAfters(method, test, statement);
        statement = withRules(method, test, statement);
        return statement;
    }

public class RunBefores extends Statement {
    private final Statement next;
    private final Object target;
    private final List<FrameworkMethod> befores;

    public RunBefores(Statement next, List<FrameworkMethod> befores, Object target) {
        this.next = next;
        this.befores = befores;
        this.target = target;
    }

    @Override
    public void evaluate() throws Throwable {
        for (FrameworkMethod before : befores) {
            before.invokeExplosively(target);
        }
        next.evaluate();
    }
}

Statement:封装了可执行的行为，如@Before，@BeforeClass，@Test等
![alt text](image.png)
方法调用：通过反射执行
public Object invokeExplosively(final Object target, final Object... params)
            throws Throwable {
        return new ReflectiveCallable() {
            @Override
            protected Object runReflectiveCall() throws Throwable {
                return method.invoke(target, params);
            }
        }.run();
    }

Result

public Result run(Runner runner) {
    Result result = new Result();
    RunListener listener = result.createListener();
    notifier.addFirstListener(listener);
    try {
        notifier.fireTestRunStarted(runner.getDescription());
        runner.run(notifier);
        notifier.fireTestRunFinished(result);
    } finally {
        removeListener(listener);
    }
    return result;
}

RunNotifier，EachTestNotifier
RunListener
Description



1.将测试类包装成Request
2.根据测试类得到Runner
3.执行Runner的public abstract void run(RunNotifier notifier);方法
4.将测试方法，before，after方法等包装成Statement,依次执行
5.各个执行节点都会调用notifier的对应方法，通知listener执行对应操作


有趣的设计
单例
listener
方法入参里的final
builder
策略模式
ReflectiveCallable


@AutoWired 注入Map