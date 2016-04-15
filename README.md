Soul – это mBaaS (mobile backend as a service) облачный сервис, позволяющий создавать и эксплуатировать приложения без необходимости написания server-side кода, поднятия собственных серверов и их поддержки. 

## Установка
Добавьте заголовочный файл в ваше приложение чтобы получить все необходимые классы:
Include the Firebase header in your app to get all the needed classes:

```obj-c
#import <SoulSDK/SoulSDK.h>
```

## Использование

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

[[soulSDK phoneAuth] getCode:phoneNumber success:^(SLPhoneAuthRequest *_Nonnull responseObject) {
    
    NSLog(@"Status: %d", responseObject.status);

} failure:^(NSError *_Nullable error) {

    NSLog(@"%@", error);

}];
```

После проверки полученного кода пользователь будет зарегистрирован

```obj-c
[[soulSDK phoneAuth] verify:phoneNumber code:code success:^(SLPhoneAuthVerify *_Nonnull responseObject) {

    NSLog(@"%@ / %@", responseObject.me.userId, responseObject.authorization.sessionToken);

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
[[soulSDK me] setFilterableParameters:filterable
                              success:^(SLMeUserResponse *_Nonnull responseObject) {

                                  NSLog(@"%@", responseObject.me.parameters.filterable);

                              }
                              failure:^(NSError *_Nullable error) {

                                  NSLog(@"%@", error);

                              }];
```

### Загружка фотографии


### Поиск партнёров