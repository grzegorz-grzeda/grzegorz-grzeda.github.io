---
layout: post
title: "Factory Method design pattern"
date: 2023-05-12 07:00:00 -0000
categories: "design patterns"
---

The `Factory Method` is just an implementation of the [`Template Method`](/posts/template-method-pattern/) but for building objects.

Let us consider an IRL example, I've recently coded. Here's the story:
I was building and [HD44780](https://www.sparkfun.com/datasheets/LCD/HD44780.pdf) [LCD driver](https://www.google.com/search?q=2x16+lcd&sxsrf=ACYBGNSKKXrj0aXwVT8r8mE9_zKFRfRc5A:1577322400652&source=lnms&tbm=isch&sa=X&ved=2ahUKEwjgiYzrj9LmAhXC5KQKHVjjBYgQ_AUoAXoECAwQAw&biw=1366&bih=625). I needed some hardware abstraction, so that I could test the rest of the software without any running hardware. Also, I didn't know if I would `bit-bang` my way through, or use some funky [I2C IO expander](http://www.ti.com/lit/ds/symlink/pcf8574.pdf) as a proxy. 

Depending on my **project configuration**, I needed an LCD driver:

```c++
struct ILcdDriver{
	//...
	virtual void sendCmd(char cmd) = 0;
	virtual void sendData(char data) = 0;
};
```

The actual drivers in question would be:
```c++
struct MockLcdDriver: ILcdDriver {};

struct GpoLcdDriver: ILcdDriver{};

struct ProxyLcdDriver: ILcdDriver{};
```

Each one behaving totally differently but giving the same end result: controlling the HD44780 (or pretending) in a proper way.

Now I needed an entity that would deliver me a proper driver, depending on the given project configuration.
```c++
struct IDeliverLcdDriver{
	// ...
	virtual ILcdDriver* deliver() = 0;
};
```

Now defining classes:
```c++
struct DeliverMockLcdDriver:IDeliverLcdDriver{};

struct DeliverGpoLcdDriver:IDeliverLcdDriver{};

struct DeliverProxyLcdDriver:IDeliverLcdDriver{};
```
I could easily generate drivers based even on `#ifdef`'s"
```c++
// somewhere in the init part of the project
ILcdDriver *drv = nullptr;

IDeliverLcdDriver *deliver = nullptr;

#if defined(LCD_GPO)
	deliver = new DeliverGpoLcdDriver;
#elif defined(LCD_PROXY)
	deliver = new DeliverProxyLcdDriver;
#else
	deliver = new DeliverMockLcdDriver;
#endif

drv = deliver->deliver();

// ...
```
