# 🚨 Ошибки

{% hint style="danger" %}
## SQLSTATE\[HY000\] \[2054\] The server requested authentication method unknown to the client
{% endhint %}

Исправление:

```text
CREATE USER 'admin'@'localhost' IDENTIFIED WITH mysql_native_password BY 'yourpass';
```



