# 如何使用 Python 和 Twilio API 发送 WhatsApp 消息？

> 原文：<https://medium.com/hackernoon/how-to-send-whatsapp-message-using-python-and-twilio-api-fc63f62154ca>

![](img/a669821ab1849face73d588c75606077.png)

# WhatsApp 的 Twilio API

WhatsApp 的 Twilio API 现已推出早期测试版，允许开发人员在沙盒环境中构建原型。Twilio 使您能够使用共享电话号码立即使用 WhatsApp API，而无需等待 WhatsApp 批准专用号码。要在生产中启用 WhatsApp API，您需要在有限可用性程序中请求访问权限。让我们继续使用 WhatsApp API 发送您的第一条消息。

首先，你需要注册一个 Twilio 帐户或使用现有帐户登录，然后转到 [Twilio whatsapp 沙盒控制台](https://www.twilio.com/console/sms/whatsapp/sandbox/)，按照屏幕上的说明并选择接收 whatsapp 消息以启用沙盒。

Twilio sandbox 允许你通过向选择加入的用户发送和接收来自共享 Twilio 号码的消息来测试 WhatsApp。

# 使用 Python 发送 WhatsApp 消息

从 [Twilio 控制台](https://www.twilio.com/console)获取您的账户 SID 和认证令牌，以认证您的 REST API 调用。

## 设置您的 python 开发环境

虽然有[官方 python sdk](https://www.twilio.com/docs/libraries/python) 可用于 twilio，但我在本教程中使用[请求](http://docs.python-requests.org/en/master/)库，因为我喜欢它。

## 使用 WhatsApp 消息模板发送消息/通知

要使用 WhatsApp 发送出站消息，您必须仅使用预先批准的模板，并且您可以在 Twilio 号码上启用 WhatsApp 时创建自定义模板。

目前，有 3 个预先批准的模板可用。

1.  约会提醒模板
2.  订单通知模板
3.  验证码模板

## 发送约会提醒

约会提醒模板对于发送约会提醒很有用。这个模板如下图所示，

```
Your appointment is coming up on {{August 20th}} at {{6:00PM}}
```

## 密码

```
import requestsTWILIO_SID = “ACXXXXXXXXXXXXXX”
TWILIO_AUTHTOKEN = “62f1efa7e0e9471cfdbfXXXXXXXXX”
TWILIO_MESSAGE_ENDPOINT = “[https://api.twilio.com/2010-04-01/Accounts/{TWILIO_SID}/Messages.json](https://api.twilio.com/2010-04-01/Accounts/{TWILIO_SID}/Messages.json)".format(TWILIO_SID=TWILIO_SID)TWILIO_NUMBER = “whatsapp:+14155238886”def send_whatsapp_message(to, message): message_data = {
        "To": to,
        "From": TWILIO_NUMBER,
        "Body": message,
    }
    response = requests.post(TWILIO_MESSAGE_ENDPOINT, data=message_data, auth=(TWILIO_SID, TWILIO_AUTHTOKEN))

    response_json = response.json()

    return response_jsonto_number = “whatsapp:+919566XXXXXX” 
appointment_msg = “””Your appointment is coming up on August 20th at 6:00PM”””msg = send_whatsapp_message(to_number, appointment_msg)print(msg[‘sid’]) # SM5xxxafa561e34b1e84c9d22351ae08a0
print(msg[‘status’]) # queued
```

## 发送订单通知

订单通知模板对于发送订单详细信息非常有用。这个模板如下图所示，

```
Your Yummy Cupcakes Company order of {{1 dozen frosted cupcakes}} 
has shipped and should be delivered on {{July 10, 2019}}. 
Details : [http://www.yummycupcakes.com/](http://www.yummycupcakes.com/)
```

## 密码

```
to_number = "whatsapp:+919566XXXXXX" order_notif_msg = """Your Yummy Cupcakes Company order of 1 dozen frosted cupcakes has shipped and should be delivered on July 10, 2019\. Details : [http://www.yummycupcakes.com/](http://www.yummycupcakes.com/)"""msg = send_whatsapp_message(to_number, order_notif_msg)
print(msg['sid'])
```

## 验证码

验证码模板适用于发送一次性密码和手机号码验证码。这个模板如下图所示，

```
Your Twilio code is {{1238432}}
```

## 密码

```
to_number = "whatsapp:+919566XXXXXX"  
otp_msg = """Your Twilio code is 1238432"""msg = send_whatsapp_message(to_number, otp_msg)
print(msg['sid'])
```

# WhatsApp 消息的 Webhooks

您可以设置 webhooks 来接收 WhatsApp 的入站消息，从而构建一个名为聊天机器人的对话代理体验。

您可以为两个事件配置 webhooks。一个是当你的 twilio whatsapp 号码收到新消息时发送的，另一个是检查使用 Twilio API 发送的消息的交付状态更新。更多信息:[https://www.twilio.com/docs/sms/whatsapp/api](https://www.twilio.com/docs/sms/whatsapp/api)

> *原贴于*[*【idiotinside.com】*](https://www.idiotinside.com/2018/08/17/send-message-whatsapp-api-python-twilio/)