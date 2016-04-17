Soul – это mBaaS (mobile backend as a service) облачный сервис, позволяющий создавать и эксплуатировать приложения без необходимости написания server-side кода, поднятия собственных серверов и их поддержки. 

## Установка
Добавьте заголовочный файл в ваше приложение чтобы получить все необходимые классы:

```obj-c
#import <SoulSDK/SoulSDK.h>
```

## Использование

Как подключать библиотеку мы расскажем на хакатоне.

### Инициализация

```obj-c
SLConfig *config = [SLConfig defaultConfig];
config.apiKey = @"*********";

[SoulSDK activateWithApiConfig:config];
```

### Авторизация по телефону

Для авторизации по номеру телефона необходимо получить код подтверждения

```obj-c
SoulSDK *soulSDK = [SoulSDK instance];
NSString *phoneNumber = @"+79061234567";

[[soulSDK phoneAuth] getCode:phoneNumber success:^(SLPhoneAuthRequest *_Nonnull response) {
    
    NSLog(@"Status: %d", response.status);

} failure:^(NSError *_Nullable error) {

    NSLog(@"%@", error);
}];
```

После проверки полученного кода пользователь будет зарегистрирован

```obj-c
[[soulSDK phoneAuth] verify:phoneNumber code:code success:^(SLPhoneAuthVerify *_Nonnull response) {

    NSLog(@"%@ / %@", response.me.userId, response.authorization.sessionToken);

} failure:^(NSError *_Nullable error) {

    NSLog(@"%@", error);
}];
```

### Профиль пользователя

В панеле администратора разработчик определяет параметры пользователей. Информация о пользователе будет использоваться для поиска подходящих партнёров. 

```obj-c
NSDictionary *filterable = @{
                             @"age": @(32),
                             @"gender": @"male",
                             @"pets": @"kitten",
                             @"car": @"bmw",
                             };

SoulSDK *soulSDK = [SoulSDK instance];
[[soulSDK me] setFilterableParameters:filterable success:^(SLMeUserResponse *_Nonnull response) {

    NSLog(@"%@", response.me.parameters.filterable);

} failure:^(NSError *_Nullable error) {

    NSLog(@"%@", error);
}];
```

### Загрузка фотографии

О создании альбомов и загрузке фтографий мы расскажем немного позже.

### Поиск партнёров

```obj-c
NSString *session = @"";
NSString *token = @"";

[[sdk users] loadBySession:session uniqueToken:token success:^(SLUsersRecsResponse *_Nonnull responce) {

    for (SLUser *user in responce.users) {
    	NSLog(@"%@", user.userId);
    }

} failure:^(NSError *_Nullable error) {

    NSLog(@"%@", error);
}];
```
Для поиска партнёров нужно определить сессию поиска и с каждым запросом передавать новый токен запроса. Если партнёров завершился неудачно, его можно повторить передав тот же токен.

### Отправка реакций

В панеле администратора разработчик определяет реакции и возможные значения для каждой реакции.

```obj-c
NSString *type = @"liking";
NSString *value = @"disliked";

NSDate *expiresDate = [NSDate date];
NSNumber *expiresTime = @([expiresDate timeIntervalSince1970]);

[[sdk user] sendReactionByType:type value:value userId:user.userId expiresTime:expiresTime success:^(SLOtherUserResponse *_Nonnull responce) {

    NSLog(@"%@", responce.user.reactions.sentByMe);

} failure:^(NSError *_Nullable error) {

    NSLog(@"%@", error);
}];
```

### Сообщения

```obj-c
SLChat *lastChat = [SLChat new];
NSNumber *limit = @(20);

[[sdk chats] loadChatsAfter:lastChat.chatId limit:limit success:^(SLChatsMany *_Nonnull responce) {

    for (SLChat *chat in responce.chats) {
        NSLog(@"%@ / %@", chat.chatId, chat.channelName);
    }

} failure:^(NSError *_Nullable error) {

    NSLog(@"%@", error);
}];
```

### Отправка сообщений

```obj-c
SLTextMessage *msg = [SLTextMessage messageWithText:@""];

[[sdk chat] sendMessage:msg success:^(SLMessage *_Nonnull msg) {

    NSLog(@"%@", msg.text);

} failure:^(NSError *_Nullable error) {

    NSLog(@"%@", error);
}];
```

### Получение сообщений

```obj-c
SLChatManager *chatManager = [SLChatManager chatByName:chat.channelName];

[chatManager subscribe:^(SLMessage *_Nonnull msg) {

    NSLog(@"%@", msg.text);

} failure:^(NSError *_Nullable error) {

    NSLog(@"%@", error);
}];
```