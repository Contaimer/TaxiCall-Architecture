1. Опис use case
Реалізовано сценарій "Створення поїздки з перевіркою доступності водія та розрахунком вартості".

2. Обрана архітектура
Використано **Hexagonal Architecture (Ports and Adapters)**. 
Ядро системи (`domain` та `usecase`) написане на чистій Java і не залежить від зовнішніх бібліотек, баз даних чи фреймворків (на кшталт Spring). Вся взаємодія із зовнішнім світом відбувається через інтерфейси у пакеті `ports`.

3. Структура пакетів
`com.taxi.domain` - чисті сутності та бізнес-виключення.
`com.taxi.ports` - інтерфейси для інфраструктури (SPI / API).
`com.taxi.usecase` - сервіс додатку, що виконує бізнес-сценарій.
`com.taxi.adapters` - інфраструктурні реалізації портов.

4. Інструкція із запуску
Для збірки та запуску проєкту використовуйте стандартний Java SDK:
```bash
javac com/taxi/Main.java
java com.taxi.Main
```
Доменна модель (domain/model/DriverStatus.java та Driver.java)javapackage com.taxi.domain.model;
```bash
public enum DriverStatus {
    AVAILABLE, BUSY
}
```
