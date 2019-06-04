# Template Kotlin research
Исследование на тему реализации простых шаблонов/макросов в Kotlin

## Подстановка через комментарии

### Генерация даггер скоупов
```kotlin
/*-@di-scope
@Scope @Retention(AnnotationRetention.RUNTIME)
annotation class $argument$Scope
$argument$Scope @Subcomponent
interface $argument$Component {
   fun inject(activity: $argument$Activity)
}
@-*/

//-@di-scope(argument=Home)
@Scope @Retention(AnnotationRetention.RUNTIME)
annotation class HomeScope
HomeScope @Subcomponent
interface HomeComponent {
   fun inject(activity: HomeActivity)
}
//@-GENERATED don't change
```

### Пример подмешивания поведения в класс
```kotlin
/*-@show-notification
private val notificationManager = NotificationManager(this)
override fun showNotification(n: Notification) = showNotification(n, 0)
override fun showNotification(n: Notification, delay: Int) {
    Log.i("$tag$", "show notification: $n")
    notificationManager.show(n, delay)
}
@-*/

class HomeActivity : BaseActivity, NotificationView {
    //-@show-notification(tag=HomeActivity)
    private val notificationManager = NotificationManager(this)
    override fun showNotification(n: Notification) = showNotification(n, 0)
    override fun showNotification(n: Notification, delay: Int) {
        Log.i("HomeActivity", "show notification: $n")
        notificationManager.show(n, delay)
    }
    //@-GENERATED don't change
}
```

## Подстановка через функцию

В отдельном файле или библиотеке описываются функции с маркированными именами (к примеру заканчивающиеся на !).
Эти функции должны отдавать строку или AST (к примеру из kotlin-poet) в один из параметров.

Для раскрытия макросов 
- отдельный компилятор будет компилировать только файлы с объявлением макросов
- искать в пользовательском коде их вызов
- вызывать нужный макрсов с переданными параметрами
- вставлять в место вызовам сгенерированный код, обромляя маркерными комментариями

Объявление макроса
```kotlin
fun `di-scope!`(name: String, callback: ((String) -> Unit)? = null) =
    callback?.invoke("""
        @Scope @Retention(AnnotationRetention.RUNTIME)
        annotation class $argument$Scope
        $argument$Scope @Subcomponent
        interface $argument$Component {
           fun inject(activity: $argument$Activity)
        }
        """.trim().replace("$argument$", name))

```

Использования
```kotlin
`di-scope!`(name = "Home")
```

Генерируемый код

```kotlin
// GENERATED don't change
@Scope @Retention(AnnotationRetention.RUNTIME)
annotation class HomeScope
HomeScope @Subcomponent
interface HomeComponent {
   fun inject(activity: HomeActivity)
}
// GENERATED don't change
```



