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

```bash
package com.taxi.domain.model;

public class Driver {
    private final String id;
    private final String name;
    private final DriverStatus status;

    public Driver(String id, String name, DriverStatus status) {
        this.id = id;
        this.name = name;
        this.status = status;
    }

    public String getId() { return id; }
    public DriverStatus getStatus() { return status; }
}
```
Порти / Інтерфейси (ports/DriverRepository.java)
```bash
package com.taxi.ports;

import com.taxi.domain.model.Driver;

public interface DriverRepository {
    Driver findClosestAvailable(String location);
}
```
Сценарій використання (usecase/CreateRideUseCase.java)Тут зосереджена головна вимога лаби — перевірка бізнес-правила та оркестрація.javapackage com.taxi.usecase;
```bash
import com.taxi.domain.model.*;
import com.taxi.domain.exception.NoAvailableDriversException;
import com.taxi.ports.*;
import java.util.UUID;

public class CreateRideUseCase {
    private final DriverRepository driverRepo;
    private final RideRepository rideRepo;
    private final PricingService pricingService;

    // Залежності передаються через конструктор (Dependency Injection)
    public CreateRideUseCase(DriverRepository driverRepo, RideRepository rideRepo, PricingService pricingService) {
        this.driverRepo = driverRepo;
        this.rideRepo = rideRepo;
        this.pricingService = pricingService;
    }

    public Ride execute(String passengerId, String startLoc, String endLoc) {
        Driver driver = driverRepo.findClosestAvailable(startLoc);
        if (driver == null || driver.getStatus() != DriverStatus.AVAILABLE) {
            throw new NoAvailableDriversException("Немає вільних водіїв поруч!");
        }

        
        double cost = pricingService.calculateCost(startLoc, endLoc);

        Ride ride = new Ride(
            UUID.randomUUID().toString(),
            passengerId,
            startLoc,
            endLoc,
            driver.getId(),
            cost
        );

        rideRepo.save(ride);
        return ride;
    }
}
```
