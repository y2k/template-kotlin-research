# Template Kotlin research
Исследование на тему реализации простых шаблонов/макросов в Kotlin

### Пример подмешивания поведения в класс
```kotlin
/*-@show-notification
private val notificationManager = NotificationManager(this)
override fun showNotification(n: Notification) = showNotification(n, 0)
override fun showNotification(n: Notification, delay: Int) {
    Log.i("$tag", "show notification: $n")
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
