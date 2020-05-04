# Xshell-OTP

Xshell 自动登录 One-time Password 跳板机

![USAGE](USAGE.gif)

## 使用

下载 jumper.js 后拖到最后，修改 `Main` 函数中的配置：

```js
function Main()
{
    // Calculate OTP token
    var totpObj = new TOTP();
    var epoch = Math.floor(new Date().getTime() / 1000 % 30);
    var elapsed = 30 - epoch

    // Adjust the sliding window
    if (elapsed <= 3 ) {
        xsh.Dialog.MsgBox("Hold on, and wait " + elapsed + " seconds!");
        xsh.Session.Sleep(elapsed * 1000 + 100);
    }

    // Calculate OTP token
    // REVISED 修改为你自己的 Google Authenticator Secret
    var otp = totpObj.getOTP("REVISED");

    // Copy the token to clipboard
    xsh.Screen.Synchronous = true;
    xsh.Screen.Send("cmd /c echo " + otp + "| clip");
    xsh.Screen.Send(String.fromCharCode(13));
    xsh.Screen.Clear();

    // Open Session
    // xsh.Session.Open("ssh://username:password@host:port")
    // 修改为你自己的 xsh 文件路径
    xsh.Session.Open("C:\Users\REVISED\Documents\NetSarang\Xshell\Sessions\jump.xsh");

    // Close the local shell
    // xsh.Session.Sleep(1000);
    xsh.Screen.Send("exit");
    xsh.Screen.Send(String.fromCharCode(13));
}
```

## 原理

使用 [Google Authenticator][1] MFA 验证机制登录跳板机，可以通过其导出的 secret 在本地计算出当前的 token。Xshell 支持运行 javascript 脚本，所以这个 token 可以直接在 Xshell 端计算完成。但是 Xshell 在处理 MFA 验证机制时，是直接弹出对话框，并不支持传统的 terminal 输入，Xshell 也并没有提供自动完成的 API，所以只能手动拷贝输入。

[1]: https://chrome.google.com/webstore/detail/authenticator/bhghoamapcdpbohphigoooaddinpkbai
