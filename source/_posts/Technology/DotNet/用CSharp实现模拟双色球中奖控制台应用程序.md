---
title: 用C#实现模拟双色球中奖控制台应用程序
date: 2022-05-02 14:04:17
updated: 2022-05-02 16:26:17
description: 用C#实现模拟双色球中奖控制台应用程序
keywords: 控制台程序，CSharp
cover: https://cdn.zhaifanhua.com/blog/img/202205030113836.gif
top_img: https://cdn.zhaifanhua.com/blog/img/202205030113836.gif
tags:
  - CSharp
  - .NET
categories:
  - CSharp
---





# 1. 前言

> 其实这是我在大一第一学期CSharp的课程设计，要求编写一个模拟双色球彩票的控制台应用程序，用以实现简单的模拟选购彩票。
>
> 但最近我重新看我的博客时感觉之前写的代码还是有很多不足之处，比如变量的命名，方法的命名，整体代码风格等差强人意，毕竟是入门是写的代码，所以今天稍微修改一下，让命名更规范，注释更明确。当然，更重要的还是：不是吧代码拿来即用，而是让刚开始入门的小伙伴能够快速地对编程感兴趣，对.net平台感兴趣，对CSharp这门语言感兴趣,我们大家共同把国内的.net发展起来。

## 1.1 你会学到什么？

简单地运用CSharp语言和.net平台，如变量、方法、类、数组、字典、系统函数、规范的注释、控制台输出、文件的读写、程序怎样设计等。

## 1.2 开发环境或运行环境

1. Win10或Win11系统，Visual Studio 2022 开发环境；
2. .net6，CSharp10；

# 2. 程序设计

## 2.1 流程图

![流程](https://cdn.zhaifanhua.com/blog/img/202205030113828.PNG)

## 2.2 有哪些功能？

1. 产生模拟奖池；
2. 生成自定义购号记录；
3. 打印数据，读取数据；
4. 界面输出优化；
5. 保护用户隐私；
6. 程序结束后可继续查看中奖记录等。

## 2.3 运行展示

### 2.3.1 双色球规则

![image-20220502234338317](https://cdn.zhaifanhua.com/blog/img/202205030113830.png)

### 2.3.2 充值或提现

![image-20220502234727383](https://cdn.zhaifanhua.com/blog/img/202205030113831.png)

### 2.3.3 自动购号

![image-202205030113832](https://cdn.zhaifanhua.com/blog/img/202205030113832.gif)

自动购号在去重后会自动排序展示出来。

### 2.3.4 手动购号

![动画](https://cdn.zhaifanhua.com/blog/img/202205030113833.gif)

可以看到，手动购号比系统购号稍微复杂了一点点，会有多次范围检测和重复检测，并且还要提示哪个球重复……完成购号后，自动购号和手动购号的记录就会在程序运行目录保存。

![image-20220502235840211](https://cdn.zhaifanhua.com/blog/img/202205030113834.png)

打开是这样的：

![image-20220503001059260](https://cdn.zhaifanhua.com/blog/img/202205030113835.png)

其实，数据保存方法经过了多次设计，最开始只是为了记录，后来做到开奖时不易拆分，一次次改进而来。设计过程如下：

```
====第1次设计====
用普通数据记录
第【01】注：红色球：01 02 03 04 05 06 蓝色球：07	【系统购号】时间：2019.06.12 22:02:01
第【02】注：红色球：06 14 15 22 29 32 蓝色球：11	【手动购号】时间：2019.06.12 22:02:07
====第2次设计====
去掉固定字符
01_01_02_03_04_05_06_07_系统购号_2019.06.12 22:02:01
02_06_14_15_22_29_32_11_手动购号_2019.06.12 22:02:07
====第3次设计====
用标记值 _
01_01_02_03_04_05_06_07_2019.06.12 22:02:01
02_06_14_15_22_29_32_11_2019.06.12 22:02:07
====第4次设计====
用标记值 N R B T
N01R020615182426B14T2022-05-02 下午 11:51:55
N02R011316172426B12T2022-05-02 下午 11:52:18
```

### 2.3.5 开奖

![动画](https://cdn.zhaifanhua.com/blog/img/202205030113836.gif)

开奖时会先检测是否有购号记录：没有记录对本程序来说开奖的意义不大，就不开奖；有开奖记录会产生开奖号码，并产生模拟奖池和模拟奖池累计奖金，从而产生奖金总额。

开奖后，按照自动购号、手动购号的次序进行对比购号记录，有中奖时，记录中将号码和相应的中奖金额便于查看，然后将奖金自动发放到账户余额。之后，删除之前的所有购号记录。

![image-20220503002344752](https://cdn.zhaifanhua.com/blog/img/202205030113837.png)

具体信息如下：

![image-20220503002516109](https://cdn.zhaifanhua.com/blog/img/202205030113838.png)

### 2.3.6 开奖后的操作

开奖后你可以继续选择充值、提现、购号等。

![image-20220503002823362](https://cdn.zhaifanhua.com/blog/img/202205030113839.png)

# 3. 核心代码

![image-20220503003429182](https://cdn.zhaifanhua.com/blog/img/202205030113840.png)

## 3.1 程序入口

### 3.1.1入口

Program.cs

```c#
// ----------------------------------------------------------------
// Copyright ©2022 ZhaiFanhua All Rights Reserved.
// FileName:Program
// Guid:372ee8df-d0d5-4ceb-b033-613860c6257c
// Author:zhaifanhua
// Email:me@zhaifanhua.com
// CreatTime:2022-05-02 上午 08:11:21
// ----------------------------------------------------------------

using System.Runtime.InteropServices;
using TwoColorBall.Main;

namespace TwoColorBall;

/// <summary>
/// 程序入口
/// </summary>
public class Program
{
    private const int _windowHeight = 50;
    private const int _windowWidth = 150;

    /// <summary>
    /// 程序开始
    /// </summary>
    /// <param name="args"></param>
    /// <exception cref="Exception"></exception>
    public static void Main(string[] args)
    {
        AppDomain.CurrentDomain.UnhandledException += UnhandledExceptionTrapper;
        try
        {
            // 设置窗口宽高
            if (RuntimeInformation.IsOSPlatform(OSPlatform.Windows))
            {
                Console.WindowHeight = _windowHeight;
                Console.WindowWidth = _windowWidth;
            }
            Console.ResetColor();
            Console.WriteLine($"\t\t\t\t Copyright (C){DateTime.Now.Year} ZhaiFanhua All Rights Reserved.");
            Console.WriteLine("你好！欢迎你进入摘繁华的模拟双色球程序！");
            Console.WriteLine("===================================================程序开始===================================================");
            // 入口标记
            int entranceMark = 1;
            while (entranceMark != 0)
            {
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("请输入以下按键选择：【Q/q】查看规则；【W/w】进入模拟；【E/e】结束程序；");
                Console.ResetColor();
                string selectKey = Console.ReadLine() ?? "";
                switch (selectKey.ToUpper())
                {
                    case "Q":
                        Console.ForegroundColor = ConsoleColor.Green;
                        Console.WriteLine("\t你选择了查看规则；");
                        Console.ResetColor();
                        Rule rule = new();
                        rule.Start();
                        break;

                    case "W":
                        Console.ForegroundColor = ConsoleColor.Green;
                        Console.WriteLine("\t你选择了进入模拟；");
                        Console.ResetColor();
                        Ball ball = new();
                        ball.Play();
                        break;

                    case "E":
                        Console.ForegroundColor = ConsoleColor.Green;
                        Console.WriteLine("\t你选择了结束程序!");
                        Console.ResetColor();
                        entranceMark = 0;
                        break;

                    default:
                        Console.ForegroundColor = ConsoleColor.Red;
                        Console.WriteLine("\t你的输入有误，请重新选择!");
                        Console.ResetColor();
                        break;
                }
            }
            Console.WriteLine("===================================================程序结束===================================================");
            Console.WriteLine("程序结束，请按任意键退出。");
            Console.WriteLine($"\t\t\t\t Copyright (C){DateTime.Now.Year} ZhaiFanhua All Rights Reserved.");
            _ = Console.ReadKey(true);
        }
        catch (Exception ex)
        {
            throw new Exception("程序出错！错误消息：\n" + ex.Message);
        }
    }

    /// <summary>
    /// 全局异常处理
    /// </summary>
    /// <param name="sender"></param>
    /// <param name="e"></param>
    public static void UnhandledExceptionTrapper(object sender, UnhandledExceptionEventArgs e)
    {
        Console.ForegroundColor = ConsoleColor.Red;
        Console.WriteLine(e.ExceptionObject.ToString());
        Console.ResetColor();
        Console.WriteLine("请按任意键退出！");
        _ = Console.ReadLine();
        Environment.Exit(1);
    }
}
```

## 3.2 Main命名空间

### 3.2.1 选择模拟开始

Ball.cs

```c#
// ----------------------------------------------------------------
// Copyright ©2022 ZhaiFanhua All Rights Reserved.
// FileName:Ball
// Guid:25182033-35b8-414c-8e5a-2d88a9677ab0
// Author:zhaifanhua
// Email:me@zhaifanhua.com
// CreatTime:2022-05-02 上午 08:20:21
// ----------------------------------------------------------------

namespace TwoColorBall.Main;

/// <summary>
/// 模拟双色球
/// </summary>
public class Ball
{
    private BallAutomatic _ballAutomatic = new();
    private BallManual _ballManual = new();
    private Lottery _lottery = new();
    private Wallet _wallet = new();

    /// <summary>
    /// 开始模拟
    /// </summary>
    public void Play()
    {
        Console.WriteLine("         =======================================模拟双色球开始=======================================");
        // 新用户充值
        Promotion();
        // 入口标记
        int entranceMark = 1;
        while (entranceMark != 0)
        {
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("请选择：【Q/q】自动购号；【W/w】手动购号；【E/e】对已购双色球开奖；【R/r】充值或提现；【T/t】返回主菜单；");
            Console.ResetColor();
            string selectKey = Console.ReadLine() ?? "";
            switch (selectKey.ToUpper())
            {
                case "Q":
                    Console.ForegroundColor = ConsoleColor.Green;
                    Console.WriteLine("\t你选择了自动购号；");
                    Console.ResetColor();
                    _ballAutomatic.Automatic();
                    break;

                case "W":
                    Console.ForegroundColor = ConsoleColor.Green;
                    Console.WriteLine("\t你选择了手动购号；");
                    Console.ResetColor();
                    _ballManual.Manual();
                    break;

                case "E":
                    Console.ForegroundColor = ConsoleColor.Green;
                    Console.WriteLine("\t你选择了对已购买双色球开奖；");
                    Console.ResetColor();
                    _lottery.Prize();
                    break;

                case "R":
                    Console.ForegroundColor = ConsoleColor.Green;
                    Console.WriteLine("\t你选择了充值或提现；");
                    Console.ResetColor();
                    _wallet.RechargeOrConsumptManual();
                    break;

                case "T":
                    Console.ForegroundColor = ConsoleColor.Green;
                    Console.WriteLine("\t你选择了返回主菜单；");
                    Console.ResetColor();
                    entranceMark = 0;
                    break;

                default:
                    Console.ForegroundColor = ConsoleColor.Red;
                    Console.WriteLine("\t你的输入有误，请重新输入!");
                    Console.ResetColor();
                    break;
            }
        }
        Console.WriteLine("         =======================================模拟双色球结束=======================================");
        Console.WriteLine();
    }

    /// <summary>
    /// 新用户赠送余额
    /// </summary>
    private void Promotion()
    {
        Console.ForegroundColor = ConsoleColor.Red;
        Console.WriteLine("\t为了你的首次体验，系统已为你赠送100.00元用于购买双色球！\n");
        _wallet.RechargeOrConsumptAutomatic((decimal)100.00);
        Console.ResetColor();
    }
}
```

### 3.2.2 规则

Rule.cs

```c#
// ----------------------------------------------------------------
// Copyright ©2022 ZhaiFanhua All Rights Reserved.
// FileName:Rule
// Guid:5c7ad5a7-2239-4327-b82f-f14df6fb1f69
// Author:zhaifanhua
// Email:me@zhaifanhua.com
// CreatTime:2022-05-02 上午 08:22:01
// ----------------------------------------------------------------

namespace TwoColorBall.Main;

/// <summary>
/// 游戏规则
/// </summary>
public class Rule
{
    /// <summary>
    /// 开始
    /// </summary>
    public void Start()
    {
        Console.ForegroundColor = ConsoleColor.Cyan;
        Console.WriteLine(@"一、玩法及说明
            双色球投注区分为红球号码区和蓝球号码区，红球号码范围为01～33，蓝球号码范围为01～16。双色球每期从33个红球中
            开出6个号码，从16个蓝球中开出1个号码作为中奖号码，双色球玩法即是竞猜开奖号码的6个红球号码和1个蓝球号码，顺
            序不限。");
        Console.WriteLine(@"二、设奖及中奖
            |------|-----------|--------|-----------------------------------------------------------------|--------|
            |      | 中奖条件  |        |                                                                 |        |
            | 奖级 |-----------|中奖说明|                          单注奖金                               |单注赔率|
            |      | 红球 |蓝球|        |                                                                 |        |
            |------|------|----|--------|-----------------------------------------------------------------|--------|
            |      |      |    |        |当奖池资金低于1亿元时，奖金总额为当期高等奖奖金的75%与奖池中累积 |        |
            |      |      |    |        |的奖金之和，单注奖金按注均分，单注最高限额封顶1000万元。         |        |
            |      |      |    |        |当奖池资金高于1亿元(含)时，奖金总额包括两部分，一部分为当期高等奖|        |
            |一等奖|RRRRRR| B  | 中6+1  |奖金的55%与奖池中累积的奖金之和，单注奖金按注均分，单注最高限额封|  ——  |
            |      |      |    |        |顶1000万元;另一部分为当期高等奖奖金的20%，单注奖金按注均分，单注 |        |
            |      |      |    |        |最高限额封顶1000万元。                                           |        |
            |------|------|----|--------|-----------------------------------------------------------------|--------|
            |二等奖|RRRRRR|    | 中6+0  |            当期高等奖奖金的25%，单注最高限额封顶500万元。       |  ——  |
            |------|------|----|--------|-----------------------------------------------------------------|--------|
            |三等奖|RRRRR | B  | 中5+1  |                   单注奖金额固定为3000元                        | 1:1500 |
            |------|------|----|--------|-----------------------------------------------------------------|--------|
            |      |RRRRR |    | 中5+0  |                                                                 |        |
            |四等奖|------|----|--------|                   单注奖金额固定为200元                         | 1:100  |
            |      |RRRRR | B  | 中4+1  |                                                                 |        |
            |------|------|----|--------|-----------------------------------------------------------------|--------|
            |      |RRRR  |    | 中4+0  |                                                                 |        |
            |五等奖|------|----|--------|                   单注奖金额固定为10元                          | 1:5    |
            |      |RRR   | B  | 中3+1  |                                                                 |        |
            |------|------|----|--------|-----------------------------------------------------------------|--------|
            |      |RR    | B  | 中2+0  |                                                                 |        |
            |      |------|----|--------|                                                                 |        |
            |六等奖|R     | B  | 中1+1  |                    单注奖金额固定为5元                          | 1:2.5  |
            |      |------|----|--------|                                                                 |        |
            |      |      | B  | 中0+1  |                                                                 |        |
            |------|------|----|--------|-----------------------------------------------------------------|--------|");
        Console.WriteLine(@"注：
            1、高等奖奖金＝奖金总额－固定奖奖金。
            2、当奖池奖金超过1亿元(含)时，下期一等奖奖金总额包括两部分，一部分为高等奖奖金的55% 与奖池奖金之和，单注
            奖金按注均分，封顶1000万元；另一部分为高等奖奖金的20%，单注奖金按注均分，封顶1000万元。
            3、奖池奖金在1亿元以下时，单注奖金封顶500万元。");
        Console.ResetColor();
        Console.WriteLine("你已查看完双色球规则!\n");
    }
}
```
### 3.2.3 账户余额

Wallet.cs

```c#
// ----------------------------------------------------------------
// Copyright ©2022 ZhaiFanhua All Rights Reserved.
// FileName:Wallet
// Guid:bbf94a5b-f7f3-4202-a11c-aba3dac239c3
// Author:zhaifanhua
// Email:me@zhaifanhua.com
// CreatTime:2022-05-02 上午 08:23:35
// ----------------------------------------------------------------

namespace TwoColorBall.Main;

/// <summary>
/// 账户钱包
/// </summary>
public class Wallet
{
    // 余额
    private static decimal _balance = 0;

    public decimal Balance
    {
        get => _balance;
        set => _balance = value >= 0 ? value : 0;
    }

    /// <summary>
    /// 手动充值或消费
    /// </summary>
    public void RechargeOrConsumptManual()
    {
        Console.ForegroundColor = ConsoleColor.Yellow;
        Console.WriteLine("请输入你的账户存取额(正为充值，负为提现)：");
        Console.ResetColor();

        decimal money = decimal.Parse(Console.ReadLine() ?? string.Format("0"));
        RechargeOrConsumptAutomatic(money);
    }

    /// <summary>
    /// 自动充值或消费
    /// </summary>
    /// <param name="money"></param>
    public void RechargeOrConsumptAutomatic(decimal money)
    {
        decimal moneyabs = Math.Abs(money);
        if (money >= 0)
        {
            Console.ForegroundColor = ConsoleColor.Green;
            Console.WriteLine("\t充值或奖励金额为：{0}元；", FormatMoneyToDecimal(moneyabs));
            Balance += moneyabs;
            Console.ResetColor();
        }
        else
        {
            if (Math.Abs(money) > Balance)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("\t账户余额不足此提现或消费；");
                Console.ResetColor();
            }
            else
            {
                Console.ForegroundColor = ConsoleColor.Green;
                Console.WriteLine("\t提现或消费金额为：{0}元；", FormatMoneyToDecimal(moneyabs));
                Balance -= moneyabs;
                Console.ResetColor();
            }
        }
        //输出账户余额
        Console.WriteLine("\t你的账户的余额为：{0}元；", FormatMoneyToDecimal(Balance));
    }

    /// <summary>
    /// 格式化金额(1234,5678.90)
    /// </summary>
    /// <param name="money"></param>
    /// <returns></returns>
    public string FormatMoneyToDecimal(decimal money)
    {
        try
        {
            string moneyStr = money.ToString();
            string moneyRes = string.Empty;
            string moneyInt = string.Empty;
            string moneyDecimal = string.Empty;
            if (moneyStr.Contains('.'))
            {
                moneyInt = moneyStr.Split('.')[0].ToString();
                moneyDecimal = "." + moneyStr.Split('.')[1].ToString();
                moneyRes = FormatMoneyToInt(moneyInt);
            }
            else
            {
                moneyRes = FormatMoneyToInt(moneyStr);
            }
            string FormatMoneyToInt(string moneyint)
            {
                if (moneyint.ToString().Length > 4)
                {
                    string moneyNotFormat = moneyint.Substring(0, moneyint.Length - 4);
                    string moneyFormat = moneyint.Substring(moneyint.Length - 4, 4);
                    if (moneyNotFormat.Length > 4)
                    {
                        return FormatMoneyToInt(moneyNotFormat) + "," + moneyFormat;
                    }
                    else
                    {
                        return moneyNotFormat + "," + moneyFormat;
                    }
                }
                else
                {
                    return moneyint;
                }
            }
            return moneyRes + moneyDecimal;
        }
        catch (Exception)
        {
            throw;
        }
    }
}
```

### 3.2.4 自动购号

BallAutomatic.cs

```c#
// ----------------------------------------------------------------
// Copyright ©2022 ZhaiFanhua All Rights Reserved.
// FileName:BallAutomatic
// Guid:8c918fa6-8fdb-486d-a214-ce7627c70fd8
// Author:zhaifanhua
// Email:me@zhaifanhua.com
// CreatTime:2022-05-02 上午 08:24:53
// ----------------------------------------------------------------

using TwoColorBall.Common;

namespace TwoColorBall.Main;

/// <summary>
/// 自动购号
/// </summary>
public class BallAutomatic
{
    private Wallet _myWallet = new();
    private WriteData _writeData = new();
    private Random _random = new();
    private int[] _balls = new int[7];

    /// <summary>
    /// 自动购号
    /// </summary>
    public void Automatic()
    {
        Console.WriteLine();
        Console.WriteLine("                    =============================自动购号开始=============================");
        int buytimes = CheckWallet();
        Buy(buytimes);
        Console.WriteLine("                    =============================自动购号结束=============================");
        Console.ForegroundColor = ConsoleColor.Yellow;
        Console.WriteLine("                                           (你可以选择继续购号或开奖)");
        Console.ResetColor();
        Console.WriteLine();
    }

    /// <summary>
    /// 确认购买注数并判断余额是否可用
    /// </summary>
    /// <returns></returns>
    private int CheckWallet()
    {
        int times = 0;
        int entranceMark = 1;
        while (entranceMark != 0)
        {
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("请输入你要购买几注双色球:");
            Console.ResetColor();
            times = int.Parse(Console.ReadLine() ?? "0");
            if ((decimal)times * 2 > _myWallet.Balance)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("\t你的账户余额不够购买{0}注双色球，请充值；", times);
                Console.ResetColor();
                _myWallet.RechargeOrConsumptManual();
            }
            else
            {
                entranceMark = 0;
                break;
            }
        }
        return times;
    }

    /// <summary>
    /// 购买下注
    /// </summary>
    /// <param name="buytimes"></param>
    private void Buy(int buytimes)
    {
        Console.ForegroundColor = ConsoleColor.Green;
        Console.WriteLine("\t系统正在为你购买【{0}】注双色球...", buytimes);
        Console.ResetColor();

        for (int sequence = 1; sequence <= buytimes; sequence++)
        {
            Console.Write("第【{0,2}】注：", sequence.ToString("D2"));
            Red();
            Blue();
            Console.Write("\t【自动购号】");
            string time = DateTime.Now.ToString();
            Console.Write("时间：{0}", time);
            _writeData.RecordBall(this.GetType().Name, sequence.ToString("D2"), _balls, time);
            Console.WriteLine();
        }
        Console.ForegroundColor = ConsoleColor.Green;
        Console.WriteLine("\t系统已为你完成购买【{0,2}】注双色球！", buytimes);
        Console.ResetColor();
        _myWallet.RechargeOrConsumptAutomatic(-(decimal)buytimes * 2);
    }

    /// <summary>
    /// 红色球
    /// </summary>
    public void Red()
    {
        int[] redballs = new int[6];
        // 重复检验
        for (int i = 0; i < redballs.Length; i++)
        {
            int ball = _random.Next(1, 34);
            for (int j = 0; j < i; j++)
            {
                while (ball == redballs[j])
                {
                    ball = _random.Next(1, 34);
                }
            }
            redballs[i] = ball;
        }
        // 数字排序
        for (int i = 0; i < redballs.Length - 1; i++)
        {
            for (int j = 0; j < redballs.Length - 1 - i; j++)
            {
                if (redballs[j] > redballs[j + 1])
                {
                    int max = redballs[j];
                    redballs[j] = redballs[j + 1];
                    redballs[j + 1] = max;
                }
            }
        }
        // 显示记录
        Console.Write("红色球：");
        for (int i = 0; i < redballs.Length; i++)
        {
            Task.Delay(50).Wait();
            Console.ForegroundColor = ConsoleColor.Red;
            Console.Write("{0,2:D2} ", redballs[i]);
            _balls[i] = redballs[i];
            Console.ResetColor();
        }
    }

    /// <summary>
    /// 蓝色球
    /// </summary>
    public void Blue()
    {
        int blueball = _random.Next(1, 17);
        Console.Write("蓝色球：");
        Task.Delay(50).Wait();
        Console.ForegroundColor = ConsoleColor.Blue;
        Console.Write("{0,2:D2} ", blueball);
        _balls[6] = blueball;
        Console.ResetColor();
    }
}
```

### 3.2.5 手动购号

BallManual.cs

```c#
// ----------------------------------------------------------------
// Copyright ©2022 ZhaiFanhua All Rights Reserved.
// FileName:BallManual
// Guid:63af13fc-5ad7-4a56-9631-85cbd96ce4bd
// Author:zhaifanhua
// Email:me@zhaifanhua.com
// CreatTime:2022-05-02 上午 08:25:58
// ----------------------------------------------------------------

using TwoColorBall.Common;

namespace TwoColorBall.Main;

/// <summary>
/// 手动购号
/// </summary>
public class BallManual
{
    private Wallet _myWallet = new();
    private WriteData _writeData = new();
    private int[] _balls = new int[7];

    /// <summary>
    /// 手动购号
    /// </summary>
    public void Manual()
    {
        Console.WriteLine();
        Console.WriteLine("                    =============================手动购号开始=============================");
        int buytimes = CheckWallet();
        Buy(buytimes);
        Console.WriteLine("                    =============================手动购号结束=============================");
        Console.ForegroundColor = ConsoleColor.Yellow;
        Console.WriteLine("                                           (你可以选择继续购号或开奖)");
        Console.ResetColor();
        Console.WriteLine();
    }

    /// <summary>
    /// 确认购买注数并判断余额是否可用
    /// </summary>
    /// <returns></returns>
    private int CheckWallet()
    {
        int times = 0;
        int entranceMark = 1;
        while (entranceMark != 0)
        {
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("请输入你要购买几注双色球:");
            Console.ResetColor();
            times = int.Parse(Console.ReadLine() ?? "0");
            if ((decimal)times * 2 > _myWallet.Balance)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("\t你的账户余额不够购买{0}注双色球，请充值；", times);
                Console.ResetColor();
                _myWallet.RechargeOrConsumptManual();
            }
            else
            {
                entranceMark = 0;
                break;
            }
        }
        return times;
    }

    /// <summary>
    /// 购买下注
    /// </summary>
    /// <param name="buytimes"></param>
    private void Buy(int buytimes)
    {
        Console.ForegroundColor = ConsoleColor.Green;
        Console.WriteLine("\t你正在手动购买【{0}】注双色球...", buytimes);
        Console.ResetColor();
        for (int sequence = 1; sequence <= buytimes; sequence++)
        {
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("请输入第[{0,2}]注你要选择的(1 - 33之间的)6个[红色球]号码：", sequence.ToString("D2"));
            Console.ResetColor();
            Red();
            Blue();
            Display(sequence);
            Console.Write("\t【手动购号】");
            string time = DateTime.Now.ToString();
            Console.Write("时间：{0}", time);
            _writeData.RecordBall(this.GetType().Name, sequence.ToString("D2"), _balls, time);
            Console.WriteLine();
        }
        Console.ForegroundColor = ConsoleColor.Green;
        Console.WriteLine("\t系统已为你完成购买【{0,2}】注双色球！", buytimes);
        Console.ResetColor();
        _myWallet.RechargeOrConsumptAutomatic(-(decimal)buytimes * 2);
    }

    /// <summary>
    /// 红色球
    /// </summary>
    private void Red()
    {
        int[] redballs = new int[6];
        for (int ballth = 0; ballth < redballs.Length; ballth++)
        {
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.Write("第{0}个红色球：", ballth + 1);
            Console.ResetColor();
            // 当前球
            int currentball = int.Parse(Console.ReadLine() ?? "0");
            // 检查
            currentball = Inspect(ballth, currentball);
            // 赋值当前球
            redballs[ballth] = currentball;
        }
        // 数字排序
        for (int i = 0; i < redballs.Length - 1; i++)
        {
            for (int j = 0; j < redballs.Length - 1 - i; j++)
            {
                if (redballs[j] > redballs[j + 1])
                {
                    int max = redballs[j];
                    redballs[j] = redballs[j + 1];
                    redballs[j + 1] = max;
                }
            }
        }
        // 赋值
        for (int i = 0; i < _balls[0..6].Length; i++)
        {
            _balls[i] = redballs[i];
        }

        // 检查数字是否符合
        int Inspect(int iballth, int icurrentball)
        {
            // 判断当前球是否在（1-33）范围内，若不是，重新输入，若是，赋值
            while (!(icurrentball >= 1 && icurrentball <= 33))
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.Write("第{0}个红色球：(输入范围错误！[{1}]超出了输入范围,请重新输入(1-33之间)的数)", iballth + 1, icurrentball);
                Console.ResetColor();
                icurrentball = int.Parse(Console.ReadLine() ?? "0");
                icurrentball = Inspect(iballth, icurrentball);
                break;
            }
            // 重复序号
            int repeatSeq = 0;
            // 重复个数
            int repeatNum = 0;
            // 重复标记
            int repeatMark = 1;
            // 判断当前数字与已选择数字是否有重复
            for (int ith = 0; ith < iballth; ith++)
            {
                if (icurrentball == redballs[ith])
                {
                    repeatSeq = ith;
                    repeatNum++;
                }
            }
            // 若有重复，则重新输入
            if (repeatNum != 0)
            {
                // 提示第几个数重复
                while (repeatMark != 0)
                {
                    string repeatTips = "除";
                    for (int i = 0; i < iballth; i++)
                    {
                        // 重复数字
                        string repeatNums = string.Empty;
                        for (int t = 0; t <= i; t++)
                        {
                            repeatNums = "[" + Convert.ToString(redballs[t]) + "]";
                        }
                        repeatTips += repeatNums;
                    }
                    Console.ForegroundColor = ConsoleColor.Red;
                    Console.Write("第{0}个红色球：(输入重复错误！你购买的第{1}个红色球已存在数字{2}，请重新输入(1-33之间)({3})的数)", iballth + 1, repeatSeq + 1, icurrentball, repeatTips);
                    icurrentball = int.Parse(Console.ReadLine() ?? "0");
                    Console.ResetColor();
                    icurrentball = Inspect(iballth, icurrentball);
                    repeatMark = 0;
                }
            }
            return icurrentball;
        }
    }

    /// <summary>
    /// 蓝色球
    /// </summary>
    private void Blue()
    {
        int blueball = 0;
        //判断输入数字是否在（1-16）范围内，若是取值，若不是重新输入
        while (!(blueball >= 1 && blueball <= 16))
        {
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("请输入你要选择的(1-16之间)1个[蓝色球]号码：");
            Console.Write("蓝色球：");
            Console.ResetColor();
            blueball = int.Parse(Console.ReadLine() ?? "0");
        }
        _balls[6] = blueball;
    }

    /// <summary>
    /// 显示记录
    /// </summary>
    private void Display(int sequence)
    {
        Console.Write("第【{0,2}】注：", sequence.ToString("D2"));
        Console.Write("红色球：");
        for (int i = 0; i < _balls[0..6].Length; i++)
        {
            Task.Delay(50).Wait();
            Console.ForegroundColor = ConsoleColor.Red;
            Console.Write("{0,2:D2} ", _balls[i]);
            Console.ResetColor();
        }
        Console.Write("蓝色球：");
        Task.Delay(50).Wait();
        Console.ForegroundColor = ConsoleColor.Blue;
        Console.Write("{0,2:D2} ", _balls[6]);
        Console.ResetColor();
    }
}
```

### 3.2.6 开奖

Lottery.cs

```c#
// ----------------------------------------------------------------
// Copyright ©2022 ZhaiFanhua All Rights Reserved.
// FileName:Lottery
// Guid:1ae901e8-4a3f-4281-a62f-a2c9a9640396
// Author:zhaifanhua
// Email:me@zhaifanhua.com
// CreatTime:2022-05-02 上午 08:38:22
// ----------------------------------------------------------------

using TwoColorBall.Common;

namespace TwoColorBall.Main;

/// <summary>
/// 开奖
/// </summary>
public class Lottery
{
    private Wallet _wallet = new();
    private ReadData _readData = new();
    private WriteData _writeData = new();
    private Random _random = new();

    // 购号记录是否存在
    private Dictionary<string, bool> _ballIsRecord = new();

    // 模拟奖池
    private decimal _lottery = 0;

    // 奖池中累积的奖金之和
    private decimal _bonusCumulative = 0;

    // 奖金总额
    private decimal _bonusTotal = 0;

    // 单次中奖金额
    private decimal _bonusAmount = 0;

    // 总中奖金额
    private decimal _bonusAmounts = 0;

    // 中奖号码
    private int[] _prizeBall = new int[7];

    // 中奖总数
    private int[] _prizeCount = new int[7];

    // 中奖等级
    private string _prizeGrade = string.Empty;

    /// <summary>
    /// 开奖入口
    /// </summary>
    public void Prize()
    {
        // 删除开奖记录
        _readData.DeleteFile(this.GetType().Name);
        _readData.DeleteFile(@"PrizeRecord");
        // 判断是否开奖
        bool isPrize = WhetherPrize();
        // 有购票记录则开奖，没有则不开奖
        if (isPrize)
        {
            Console.ForegroundColor = ConsoleColor.Green;
            Console.Write("\t存在购号记录，即将开奖！\n");
            Console.ResetColor();
            Console.WriteLine();
            Console.WriteLine("                                ================模拟开奖开始================");
            StartPrize();
            ComparativeData();
            Console.WriteLine("                                ================模拟开奖结束================");
            Console.WriteLine();
        }
        else
        {
            Console.ForegroundColor = ConsoleColor.Green;
            Console.Write("\t你没有任何形式的购号记录，请先购号后开奖！\n");
            Console.ResetColor();
            Console.WriteLine();
        }
        // 删除购号记录
        _readData.DeleteFile(typeof(BallAutomatic).Name);
        _readData.DeleteFile(typeof(BallManual).Name);
    }

    /// <summary>
    /// 判断是否开奖
    /// </summary>
    /// <returns></returns>
    private bool WhetherPrize()
    {
        // 删除开奖前创建的模拟开奖历史记录
        _readData.DeleteFile(this.GetType().Name);
        Console.WriteLine();
        Console.ForegroundColor = ConsoleColor.Green;
        Console.WriteLine("\t正在判断是否开奖...");
        Console.ResetColor();
        // 判断购号记录是否存在
        _ballIsRecord = FindData();
        bool isFind = _ballIsRecord.ToList().Any(e => e.Value == true);
        return isFind;
    }

    /// <summary>
    /// 判断是否存在购号记录
    /// </summary>
    /// <returns></returns>
    private Dictionary<string, bool> FindData()
    {
        Console.ForegroundColor = ConsoleColor.Green;
        Console.Write("\t正在判断是否存在购号记录...");
        Console.ResetColor();
        Dictionary<string, bool> isFind = new Dictionary<string, bool>
        {
            { typeof(BallAutomatic).Name,false},
            { typeof(BallManual).Name,false}
        };
        return _readData.FindData(isFind);
    }

    /// <summary>
    /// 产生开奖号
    /// </summary>
    private void StartPrize()
    {
        Console.ForegroundColor = ConsoleColor.Green;
        Console.WriteLine("\t正在产生开奖号码...");
        Console.ResetColor();
        BallAutomatic myBallAutomatic = new BallAutomatic();
        Console.Write("【开奖号码】");
        Red();
        Blue();
        Console.Write("\t【模拟开奖】");
        string time = DateTime.Now.ToString();
        Console.Write("时间：{0}", time);
        Console.WriteLine();
        Console.WriteLine("\t开奖号码已产生!");
        Console.WriteLine();
        Console.ForegroundColor = ConsoleColor.Green;
        Console.Write("\t正在计算奖池金额...");
        Console.ResetColor();
        // 奖池
        _lottery = _random.Next(10000000, 999999999);
        // 奖池中累积的奖金之和
        _bonusCumulative = _random.Next(1000000, 99999999);
        // 奖金总额
        _bonusTotal = _lottery + _bonusCumulative;
        Task.Delay(1000).Wait();
        Console.Write("\t本次开奖奖池金额为：{0}元;奖池中累积的奖金之和为：{1}元;奖金总额为：{2}元;",
             _wallet.FormatMoneyToDecimal(_lottery),
             _wallet.FormatMoneyToDecimal(_bonusCumulative),
             _wallet.FormatMoneyToDecimal(_bonusTotal));
        Console.WriteLine();
        // 打印模拟开奖记录
        _writeData.RecordLottery(this.GetType().Name, _prizeBall, time,
            _wallet.FormatMoneyToDecimal(_lottery),
            _wallet.FormatMoneyToDecimal(_bonusCumulative),
            _wallet.FormatMoneyToDecimal(_bonusTotal));
    }

    /// <summary>
    /// 红色球
    /// </summary>
    public void Red()
    {
        int[] redballs = new int[6];
        // 重复检验
        for (int i = 0; i < redballs.Length; i++)
        {
            int ball = _random.Next(1, 34);
            for (int j = 0; j < i; j++)
            {
                while (ball == redballs[j])
                {
                    ball = _random.Next(1, 34);
                }
            }
            redballs[i] = ball;
        }
        // 数字排序
        for (int i = 0; i < redballs.Length - 1; i++)
        {
            for (int j = 0; j < redballs.Length - 1 - i; j++)
            {
                if (redballs[j] > redballs[j + 1])
                {
                    int max = redballs[j];
                    redballs[j] = redballs[j + 1];
                    redballs[j + 1] = max;
                }
            }
        }
        // 显示记录
        Console.Write("红色球：");
        for (int i = 0; i < redballs.Length; i++)
        {
            Task.Delay(50).Wait();
            Console.ForegroundColor = ConsoleColor.Red;
            Console.Write("{0,2:D2} ", redballs[i]);
            _prizeBall[i] = redballs[i];
            Console.ResetColor();
        }
    }

    /// <summary>
    /// 蓝色球
    /// </summary>
    public void Blue()
    {
        int blueball = _random.Next(1, 17);
        Console.Write("蓝色球：");
        Task.Delay(50).Wait();
        Console.ForegroundColor = ConsoleColor.Blue;
        Console.Write("{0,2:D2} ", blueball);
        _prizeBall[6] = blueball;
        Console.ResetColor();
    }

    /// <summary>
    /// 对比数据
    /// </summary>
    public void ComparativeData()
    {
        Console.ForegroundColor = ConsoleColor.Green;
        Console.WriteLine("\t正在查找数据，请稍候...");
        Console.ResetColor();
        Console.WriteLine("                                        ========查看中奖开始========");
        // 查找数据
        foreach (var br in _ballIsRecord)
        {
            string tip = br.Key == typeof(BallAutomatic).Name ? "系统购号" : "手动购号";
            if (br.Value)
            {
                Console.ForegroundColor = ConsoleColor.Green;
                Console.WriteLine($"\t查找到{tip}数据，正在读取...");
                Console.ResetColor();
                JudgeData(br.Key);
            }
            else
            {
                Console.WriteLine($"\t你没有{tip}数据！");
            }
        }
        // 判断总中奖数
        if (_prizeCount[6] == 0)
        {
            Console.WriteLine("\t抱歉，你没有中奖！");
        }
        else
        {
            Console.WriteLine($"\t你共中奖{_prizeCount[6]}注，获得总奖金：{_bonusAmounts}元！其中：" +
                $"一等奖{_prizeCount[0]}注；" +
                $"二等奖{_prizeCount[1]}注；" +
                $"三等奖{_prizeCount[2]}注；" +
                $"四等奖{_prizeCount[3]}注；" +
                $"五等奖{_prizeCount[4]}注；" +
                $"六等奖{ _prizeCount[5]}注；");
        }
        _wallet.RechargeOrConsumptAutomatic(_bonusAmounts);
        _writeData.RecordPrizeAmounts(_prizeCount, _wallet.FormatMoneyToDecimal(_bonusAmounts));
        Console.WriteLine("                                        ========查看中奖结束========");
        // 总中奖数清零
        Array.Clear(_prizeCount, 0, _prizeCount.Length);
        // 总中奖金额清零
        _bonusAmounts = 0;
    }

    /// <summary>
    /// 判断数据
    /// </summary>
    /// <param name="name"></param>
    public void JudgeData(string name)
    {
        // 读取行数
        int rows = _readData.GetRowsCount(name);
        // 提示
        string tip = name == typeof(BallAutomatic).Name ? "系统购号" : "手动购号";
        for (int everyrow = 1; everyrow <= rows; everyrow++)
        {
            // 读取数据
            string[] datastr = new string[4];
            datastr = _readData.ReadBallData(name, everyrow);
            // 定义一个读取文件后数组，用于比较是否中奖
            int[] databall = new int[7];
            // 拆分后的序号
            string sequence = datastr[1];
            // 拆分后的红球
            string redballstr = datastr[2];
            for (int i = 0; i < 6; i++)
            {
                databall[i] = int.Parse(redballstr.Substring(i * 2, 2));
            }
            // 拆分后的蓝球
            databall[6] = int.Parse(datastr[3]);
            // 拆分后的时间
            string time = datastr[4];
            // 开奖数字对比之后计数
            int red = 0;
            int blue = 0;
            for (int i = 0; i < _prizeBall.Length; i++)
            {
                if (_prizeBall[i] == databall[i])
                {
                    _ = i < 6 ? red++ : blue++;
                }
            }
            // 判断中奖
            JudgePrize(sequence, databall, time, tip, red, blue);
        }
    }

    /// <summary>
    /// 判断中奖
    /// </summary>
    /// <param name="sequence"></param>
    /// <param name="databall"></param>
    /// <param name="time"></param>
    /// <param name="tip"></param>
    /// <param name="red"></param>
    /// <param name="blue"></param>
    public void JudgePrize(string sequence, int[] databall, string time, string tip, int red, int blue)
    {
        // 是否输出
        bool output = false;
        if (red == 6 && blue == 1)
        {
            _prizeGrade = "一";
            _bonusAmount = _bonusTotal * 0.75m;
            if (_bonusAmount >= 10000000)
            {
                _bonusAmount = 10000000;
            }
            _prizeCount[6]++;
            _prizeCount[0]++;
            output = true;
        }
        if (red == 6 && blue == 0)
        {
            _prizeGrade = "二";
            _bonusAmount = _bonusTotal * 0.25m;
            if (_bonusAmount >= 10000000)
            {
                _bonusAmount = 5000000;
            }
            _prizeCount[6]++;
            _prizeCount[1]++;
            output = true;
        }
        if (red == 5 && blue == 1)
        {
            _prizeGrade = "三";
            _bonusAmount = 3000;
            _prizeCount[6]++;
            _prizeCount[2]++;
            output = true;
        }
        if ((red == 5 && blue == 0) || (red == 4 && blue == 1))
        {
            _prizeGrade = "四";
            _bonusAmount = 200;
            _prizeCount[6]++;
            _prizeCount[3]++;
            output = true;
        }
        if ((red == 4 && blue == 0) || (red == 3 && blue == 1))
        {
            _prizeGrade = "五";
            _bonusAmount = 10;
            _prizeCount[6]++;
            _prizeCount[4]++;
            output = true;
        }
        if ((red == 2 && blue == 1) || (red == 1 && blue == 1) || (red == 0 && blue == 1))
        {
            _prizeGrade = "六";
            _bonusAmount = 5;
            _prizeCount[6]++;
            _prizeCount[5]++;
            output = true;
        }
        if (output)
        {
            _bonusAmounts += _bonusAmount;
            // 中奖输出
            Console.Write("第【{0}】注：红色球：", sequence);
            Console.ForegroundColor = ConsoleColor.Red;
            for (int i = 0; i < 6; i++)
            {
                Console.Write("{0,2:D2} ", databall[i]);
            }
            Console.ResetColor();
            Console.Write("蓝色球：");
            Console.ForegroundColor = ConsoleColor.Blue;
            Console.Write("{0,2:D2}", databall[6]);
            Console.ResetColor();
            Console.Write("\t【{0}】", tip);
            Console.Write("时间：{0}", time);
            Console.Write("\t[该注中了{0}等奖！获得奖金：{1}元；]", _prizeGrade, _wallet.FormatMoneyToDecimal(_bonusAmount));
            _writeData.RecordPrize(sequence, databall, time, tip, _prizeGrade, _wallet.FormatMoneyToDecimal(_bonusAmount));
            Console.WriteLine();
        }
    }
}
```

## 3.3 Common命名空间

### 3.3.1 写数据

WriteData.cs

```c#
// ----------------------------------------------------------------
// Copyright ©2022 ZhaiFanhua All Rights Reserved.
// FileName:WriteData
// Guid:c426bd62-1823-4187-8390-904a85be9d62
// Author:zhaifanhua
// Email:me@zhaifanhua.com
// CreatTime:2022-05-02 上午 08:59:57
// ----------------------------------------------------------------

namespace TwoColorBall.Common;

/// <summary>
/// 记录数据
/// </summary>
public class WriteData
{
    /// <summary>
    /// 购号记录
    /// </summary>
    /// <param name="name"></param>
    /// <param name="sequence"></param>
    /// <param name="balls"></param>
    /// <param name="time"></param>
    public void RecordBall(string name, string sequence, int[] balls, string time)
    {
        // 标记值
        const string _sequence = "N";
        const string _red = "R";
        const string _blue = "B";
        const string _time = "T";
        string filename = name + ".txt";
        using (StreamWriter file = File.AppendText(filename))
        {
            string red = string.Empty;
            for (int i = 0; i < balls[0..6].Length; i++)
            {
                red += balls[i].ToString("D2");
            }
            string blue = balls[6].ToString("D2");
            string record = _sequence + sequence + _red + red + _blue + blue + _time + time;
            file.WriteLine(record);
        }
    }

    /// <summary>
    /// 开奖记录
    /// </summary>
    /// <param name="name"></param>
    /// <param name="balls"></param>
    /// <param name="time"></param>
    /// <param name="lottery"></param>
    public void RecordLottery(string name, int[] balls, string time, string lottery, string bonusCumulative, string bonusTotal)
    {
        string filename = name + ".txt";
        using (StreamWriter file = File.AppendText(filename))
        {
            string red = string.Empty;
            for (int i = 0; i < balls[0..6].Length; i++)
            {
                red += balls[i].ToString("D2") + " ";
            }
            string blue = balls[6].ToString("D2");
            string record = "红色球：" + red + "蓝色球：" + blue;
            file.Write("【开奖号码】{0}", record);
            file.Write("\t【模拟开奖】时间：{0}", time);
            file.WriteLine();
            file.Write("\t本次开奖奖池金额为：{0}元;奖池中累积的奖金之和为：{1}元;奖金总额为：{2}元;", lottery, bonusCumulative, bonusTotal);
            file.WriteLine();
        }
    }

    /// <summary>
    /// 中奖记录和金额记录
    /// </summary>
    /// <param name="num"></param>
    /// <param name="ball"></param>
    /// <param name="time"></param>
    /// <param name="whichball"></param>
    public void RecordPrize(string num, int[] ball, string time, string whichball, string prizeGrade, string prizeAmount)
    {
        string filename = @"PrizeRecord.txt";
        using (StreamWriter file = File.AppendText(filename))
        {
            file.Write("第【{0}】注：红色球：", num);
            for (int i = 0; i < 6; i++)
            {
                file.Write("{0,2:D2} ", ball[i]);
            }
            file.Write("蓝色球：{0,2:D2}", ball[6]);
            file.Write("\t【{0}】", whichball);
            file.Write("时间：{0}", time);
            file.Write("\t\t[该注中了{0}等奖！获得奖金：{1}元；]", prizeGrade, prizeAmount);
            file.WriteLine();
        }
    }

    /// <summary>
    /// 中奖概括记录
    /// </summary>
    /// <param name="name"></param>
    /// <param name="prizeCount"></param>
    /// <param name="prizeAmounts"></param>
    public void RecordPrizeAmounts(int[] prizeCount, string prizeAmounts)
    {
        string filename = @"PrizeRecord.txt";
        using (StreamWriter file = File.AppendText(filename))
        {
            file.WriteLine($"\t你共中奖{prizeCount[6]}注，获得总奖金：{prizeAmounts}元！其中：" +
                $"一等奖{prizeCount[0]}注；" +
                $"二等奖{prizeCount[1]}注；" +
                $"三等奖{prizeCount[2]}注；" +
                $"四等奖{prizeCount[3]}注；" +
                $"五等奖{prizeCount[4]}注；" +
                $"六等奖{ prizeCount[5]}注；");
        }
    }
}
```

### 3.3.2 读数据

ReadData.cs

```c#
// ----------------------------------------------------------------
// Copyright ©2022 ZhaiFanhua All Rights Reserved.
// FileName:ReadData
// Guid:55cd3ffd-3af8-418a-a091-5eb3f6250fb5
// Author:zhaifanhua
// Email:me@zhaifanhua.com
// CreatTime:2022-05-02 上午 08:41:21
// ----------------------------------------------------------------

namespace TwoColorBall.Common;

/// <summary>
/// 读取数据
/// </summary>
public class ReadData
{
    /// <summary>
    /// 查找文件
    /// </summary>
    /// <param name="filename"></param>
    public bool FindFile(string name)
    {
        string filename = name + ".txt";
        return File.Exists(filename);
    }

    /// <summary>
    /// 删除文件
    /// </summary>
    /// <param name="name"></param>
    public void DeleteFile(string name)
    {
        string filename = name + ".txt";
        if (File.Exists(filename))
        {
            File.Delete(filename);
        }
    }

    /// <summary>
    /// 查找记录
    /// </summary>
    /// <param name="ballbuys"></param>
    /// <returns></returns>
    public Dictionary<string, bool> FindData(Dictionary<string, bool> ballbuys)
    {
        return ballbuys.ToDictionary(e => e.Key, e => FindFile(e.Key));
    }

    /// <summary>
    /// 获取行数
    /// </summary>
    /// <param name="name"></param>
    /// <returns></returns>
    public int GetRowsCount(string name)
    {
        string filename = name + ".txt";
        int rowsCount = 0;
        using (StreamReader read = File.OpenText(filename))
        {
            string result = read.ReadToEnd();
            rowsCount = result.Split('\n').Length - 1;
        }
        return rowsCount;
    }

    /// <summary>
    /// 读取数据
    /// </summary>
    /// <param name="name"></param>
    /// <param name="row"></param>
    public string[] ReadBallData(string name, int row)
    {
        string filename = name + ".txt";
        string[] data = File.ReadAllLines(filename);
        string[] datastr = data[row - 1].Split(new char[4] { 'N', 'R', 'B', 'T' });
        return datastr;
    }
}
```



# 4. 源码分享

## 4.1 源码分享

github（国际）：[源码分享](https://github.com/zhaifanhua/DotNet/tree/main/Demo4_TwoColorBall)

gitlab（国际）：[源码分享](https://gitlab.com/zhaifanhua/DotNet/-/tree/main/Demo4_TwoColorBall)

gitee（国内）：[源码分享](https://gitee.com/zhaifanhua/DotNet/tree/main/Demo4_TwoColorBall)



# 5. 最后

如果你觉得这篇文章还不错，请动动小指头点赞、收藏和关注哦！若本文带给你很大帮助，也可以打赏博主一杯可乐。

摘繁华版权所有，转发或引用请附上原文链接哦！