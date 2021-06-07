# Fiddler 显示请求耗时

打开 Fiddler，点击 Rules -> Customize Rules...，在弹出的窗口找到 class Handlers，然后将下列代码添加进去就可以了。

```java
// 显示每行请求的发起时间:时分秒毫秒
public static BindUIColumn("BeginTime", 80)
function BeginTimingCol(oS: Session){   
    return oS.Timers.ClientDoneRequest.ToString("HH:mm:ss.fff");
}

// 显示每行请求的响应时间:时分秒毫秒
public static BindUIColumn("EndTime", 80)
function EndTimingCol(oS: Session){     
    return oS.Timers.ServerDoneResponse.ToString("HH:mm:ss.fff");
}

// 显示每行请求的服务端耗时时间
public static BindUIColumn("Time Taken", 80)
function CalcTimingCol(oS: Session){  
    var sResult = "0";                  
    if ((oS.Timers.ServerDoneResponse > oS.Timers.ClientDoneRequest)) {  
        sResult = (oS.Timers.ServerDoneResponse - oS.Timers.ClientDoneRequest).TotalMilliseconds.ToString("N0");
    }  
    return sResult + "ms";  
}  

// 显示每行请求的服务器IP
public static BindUIColumn("ServerIP", 80)
function xxxdColumn(oS: Session): String {
    return oS["X-HostIP"];
}

// 显示每行请求的METHOD
public static BindUIColumn("Method", 40)
function FillMethodColumn(oS: Session): String {
    return oS.RequestMethod;             
}
```