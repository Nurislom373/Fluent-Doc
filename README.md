# Fluent

This Fluent library makes it very easy for you to write telegram bot with Spring framework. Creating a Telegram bot is 
very easy and versatile.

## Download

### Maven

```xml
<dependency>
    <groupId>io.github.nurislom373</groupId>
    <artifactId>spring-boot-starter-fluent</artifactId>
    <version>1.0.0</version>
</dependency>
```

### Gradle

```groovy
implementation group: 'io.github.nurislom373', name: 'spring-boot-starter-fluent', version: '1.0.0'
```

## Example

The first thing you need to do after adding the dependency is to configure the bot token and username

```yaml
fluent:
  bot:
    token: <your bot token>
    username: <your bot username>
    process-type: update
```
After adding the token and username, the only thing you need to do is create a bot controller

```java
@UpdateController
public class FluentController {

    @HandleMessage(value = "/fluent")
    void fluent(Update update, AbsSender sender) throws TelegramApiException {
        String text = update.getMessage().getText();
        SendMessage message = new SendMessage(update.getMessage().getChatId().toString(), text);
        sender.execute(message);
    }

    @HandleAny(type = HandleType.MESSAGE)
    private void handleAnyMessages(Update update, AbsSender sender) throws TelegramApiException {
        String text = "I'm handle any messages";
        SendMessage message = new SendMessage(update.getMessage().getChatId().toString(), text);
        sender.execute(message);
    }

    @HandleCallback(values = {"EN", "RU", "UZ"})
    private void handleCallback(Update update, AbsSender sender) throws TelegramApiException {
        String text = "Callback handler!";
        SendMessage message = new SendMessage(update.getMessage().getChatId().toString(), text);
        sender.execute(message);
    }

    @HandleMessage(value = "START_WITH('/fluent', value)", scope = MatchScope.EXPRESSION)
    public void handleStartWithFluent(Update update, AbsSender sender) throws TelegramApiException {
        String text = "Handle Update With Expression";
        SendMessage message = new SendMessage(update.getMessage().getChatId().toString(), text);
        sender.execute(message);
    }

    @HandleMessage(value = "START_WITH('a', value) && END_WITH('z', value)", scope = MatchScope.EXPRESSION)
    public void handleStartWithA(Update update, AbsSender sender) throws TelegramApiException {
        SendMessage message = new SendMessage(update.getMessage().getChatId().toString(), "...");
        message.setReplyToMessageId(update.getMessage().getMessageId());
        sender.execute(message);
    }

    @HandleMessage(value = "/send_username {name:[a-z]}", scope = MatchScope.VAR_EXPRESSION)
    public void handleVarExpression(Update update, AbsSender sender, @BotVariable("name") String name) throws TelegramApiException {
        String text = "Hello ".concat(name).concat("!");
        SendMessage message = new SendMessage(update.getMessage().getChatId().toString(), text);
        sender.executeAsync(message);
    }

}
```

If exceptions occur after you run the bot, they are no problem to handle.

```java
@ExceptionController
public class UpdateResourceImpl  {

    @HandleException({RuntimeException.class})
    void test(Update update, Throwable throwable, AbsSender sender) throws TelegramApiException {
        String text = "I'm Handle Exception : " + throwable.getMessage();
        SendMessage message = new SendMessage(UpdateUtils.getUserId(update).toString(), text);
        sender.execute(message);
    }

}
```

## License

MIT License

Copyright (c) 2023 Nurislom

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
