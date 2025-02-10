# 📌 **Ionic Chat App — Клиентское приложение**

Это клиентская часть чата на **Vue 3 + Ionic + SignalR**, которая подключается к серверу на **ASP.NET Core**.  
Этот файл содержит пошаговую инструкцию по установке зависимостей и запуску проекта.

---

## ⚡ **1. Установка зависимостей**

Перед началом убедитесь, что у вас установлены:

- **Node.js** (рекомендуемая версия 18+)
- **npm** (идёт в комплекте с Node.js)
- **Ionic CLI** (установим далее)
- **Git** (если хотите работать с репозиторием)

### 🚀 Устанавливаем `Ionic CLI`:

```
npm install -g @ionic/cli
```

### Устанавливаем зависимости проекта:

```
npm install
```

## ⚡ **2. Запуск клиентского приложения**

### Разработка (локальный сервер):

```
npm run dev
```

### После этого откройте в браузере:

`http://localhost:8100`

## Решение возможных проблем

### ❌ Ошибка CORS policy

Если клиент не может подключиться к серверу (Access to fetch has been blocked by CORS), убедитесь, что на бэкенде включена поддержка CORS.

В ASP.NET Core добавьте в Program.cs:

```
var corsPolicy = "_myCorsPolicy";
builder.Services.AddCors(options =>
{
    options.AddPolicy(corsPolicy, builder =>
    {
        builder.WithOrigins("http://localhost:8100") // Разрешаем фронтенду доступ
               .AllowAnyHeader()
               .AllowAnyMethod()
               .AllowCredentials();
    });
});

var app = builder.Build();
```

```
app.UseCors(corsPolicy);
app.UseRouting();
```

[Источник](https://learn.microsoft.com/ru-ru/aspnet/core/signalr/javascript-client?view=aspnetcore-9.0&tabs=visual-studio)
