---
uuid: c2c4d040-0138-11f0-9842-05fd73b7db81
title: 基于行为的测试-> Easymock(单元测试) 例子1
date: 2025-03-15 01:59:34
tags: [Java, Easymock, 单元测试]
categories: [技术]
---

最近学到了对代码进行测试，学到了黑白盒测试，基于行为，基于状态的测试..

记录一下自己的学习状态

---

# 示例代码

```
public class Premio {
    private static final float PROBABILIDAD_PREMIO = 0.1f;
    public Random generador = new Random(System.currentTimeMillis());
    public ClienteWebService cliente = new ClienteWebService();

    public String compruebaPremio() {
        if (generaNumero() < PROBABILIDAD_PREMIO) {
            try {
                String premio = cliente.obtenerPremio();
                return "Premiado con " + premio;
            } catch (ClienteWebServiceException e) {
                return "No se ha podido obtener el premio";
            }
        } else {
            return "Sin premio";
        }
    }

    // Genera numero aleatorio entre 0 y 1
    public float generaNumero() {
        return generador.nextFloat();
    }
}
```

如果我们想要对Premio类的compruebaPremio方法进行测试的话应该怎么做呢？在用Easymock框架的情况下

# 基于行为

先来介绍一下什么是基于行为。简单来说，就是通过模拟一个对象，来记录这个对象预期的行为，然后去回放（replay）这个模拟对象。进入到实际的对象调用，最后去验证（verify）这个对象所有的调用都已经发生过一次，并且没有额外的调用。

在上方的代码示例中，要怎么去写我们的测试呢？

---

首先观察到有两个对象为public，分别是*Random* 和 *ClienteWebService*，因为是public可以不用去写依赖注入，直接在这个赋值就好了。但是生产环境不能这么写，有安全问题。

# EasyMock

通过EasyMock我们来模拟这两个对象，然后对赋值。具体的操作我们可以这么做

```
@BeforeEach
void setUp() {
        generadorMock = createStrictMock(Random.class);
        clienteMock = createStrictMock(ClienteWebService.class);

        premio = new Premio();
        premio.generador = generadorMock;
        premio.cliente = clienteMock;
    }
```

到这里我们就已经成功的分别对这两个对象进行了模拟，后面我们就可以开始编写我们的测试了。

# String premio = cliente.obtenerPremio();的测试

在代码中可以看到，如果想要进入到这一行代码，那么Ramdon对象生成的值就得要小于0.1，我们当然不可能一次一次的运行靠运气去进入到这一行代码，要上一点黑科技。直接修改Random对象的值，具体的操作就需要用到EasyMock。

看代码

```
@Test
    void C1_A() {
        expect(generadorMock.nextFloat()).andReturn(0.07f);
        assertDoesNotThrow(() -> expect(clienteMock.obtenerPremio()).andReturn("entrada final Champions"));
        replay(generadorMock, clienteMock);

        String result = premio.compruebaPremio();
        verify(generadorMock, clienteMock);

        assertEquals("Premiado con entrada final Champions", result);
    }
```
   
在第一行中，我们期望Random对象的值为0.07，这样子就能进入if。这里老师一直说在**进行代码测试的时候，不能修改方法签名，也就是throw exception，也不能使用try catch。这是被严厉禁止的**    

那么为了满足这两个条件，用一个assertDoesNotThrow然后里面带一个expect模拟，就可以解决了。

到此已经完成了对两个对象的模拟，用replay来进入回放模式。

现在我们需要开始调用实际的对象会发生什么了。最后用verify验证一下。

写好了用运行验证一下

![](https://img.164314.xyz/2025/03/a5ce35e452b3e23f2bfafe1dc07af548.png)

没有问题。

后面可以在进行两个测试，也就是catch到异常和进入else分支

直接给出代码

```
@Test
    void C2_B() {
        expect(generadorMock.nextFloat()).andReturn(0.05f);
        assertDoesNotThrow(() -> expect(clienteMock.obtenerPremio()).andThrow(new ClienteWebServiceException("Error")));
        replay(generadorMock, clienteMock);

        String result = premio.compruebaPremio();
        verify(generadorMock, clienteMock);

        assertEquals("No se ha podido obtener el premio", result);
    }

    @Test
    void C3_C() {
        expect(generadorMock.nextFloat()).andReturn(0.48f);
        replay(generadorMock, clienteMock);

        String result = premio.compruebaPremio();

        verify(generadorMock, clienteMock);
        assertEquals("Sin premio", result);
    }
```


---

写完了脑子还是模模糊糊的，可能还需要时间沉淀一下吧。