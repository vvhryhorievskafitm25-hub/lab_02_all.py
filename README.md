
from abc import ABC, abstractmethod


# ============================================================
# ЗАВДАННЯ 1. КЛАСОВА ІЄРАРХІЯ, НАСЛІДУВАННЯ ТА ПОЛІМОРФІЗМ
# Предметна область: транспортні засоби
# ============================================================

class Vehicle:
    """Базовий клас транспортного засобу."""

    def __init__(self, brand, model):
        self.brand = brand
        self.model = model

    def start_engine(self):
        """Метод, який перевизначається у підкласах."""
        return "Транспортний засіб запускається."

    def __str__(self):
        return f"{self.brand} {self.model}"


class Car(Vehicle):
    def __init__(self, brand, model, seats):
        super().__init__(brand, model)
        self.seats = seats

    def start_engine(self):
        return f"Автомобіль {self} запускає двигун ключем."


class Motorcycle(Vehicle):
    def __init__(self, brand, model, engine_volume):
        super().__init__(brand, model)
        self.engine_volume = engine_volume

    def start_engine(self):
        return (
            f"Мотоцикл {self} запускає двигун кнопкою. "
            f"Об'єм двигуна: {self.engine_volume} см³."
        )


class Bus(Vehicle):
    def __init__(self, brand, model, passenger_capacity):
        super().__init__(brand, model)
        self.passenger_capacity = passenger_capacity

    def start_engine(self):
        return (
            f"Автобус {self} запускає двигун для перевезення "
            f"{self.passenger_capacity} пасажирів."
        )


def task_1():
    print("\n" + "=" * 60)
    print("ЗАВДАННЯ 1. НАСЛІДУВАННЯ ТА ПОЛІМОРФІЗМ")
    print("=" * 60)

    vehicles = [
        Car("Toyota", "Corolla", 5),
        Motorcycle("Honda", "CBR", 600),
        Bus("Mercedes-Benz", "Sprinter", 20)
    ]

    print("\nПоліморфний обхід об'єктів:")
    for vehicle in vehicles:
        print(vehicle.start_engine())

    print("\nПеревірка типів:")
    print("Чи є автомобіль об'єктом Vehicle:",
          isinstance(vehicles[0], Vehicle))
    print("Чи є мотоцикл об'єктом Motorcycle:",
          isinstance(vehicles[1], Motorcycle))
    print("Чи є Car підкласом Vehicle:",
          issubclass(Car, Vehicle))
    print("Чи є Bus підкласом Car:",
          issubclass(Bus, Car))


# ============================================================
# ЗАВДАННЯ 2. ІНКАПСУЛЯЦІЯ ТА PROPERTY
# Предметна область: банківський рахунок
# ============================================================

class BankAccount:
    """Банківський рахунок із контрольованим доступом до балансу."""

    def __init__(self, owner, balance):
        self._owner = owner  # Захищений атрибут
        self.balance = balance  # Значення проходить через setter

    @property
    def balance(self):
        """Getter приватного атрибута."""
        return self.__balance

    @balance.setter
    def balance(self, value):
        """Setter із перевіркою типу та допустимого значення."""
        if isinstance(value, bool) or not isinstance(value, (int, float)):
            raise TypeError("Баланс повинен бути числом.")

        if value < 0:
            raise ValueError("Баланс не може бути від'ємним.")

        self.__balance = value

    @property
    def annual_bonus(self):
        """Обчислювана властивість: 5% від поточного балансу."""
        return self.__balance * 0.05

    def deposit(self, amount):
        if isinstance(amount, bool) or not isinstance(amount, (int, float)):
            raise TypeError("Сума поповнення повинна бути числом.")

        if amount <= 0:
            raise ValueError("Сума поповнення повинна бути більшою за нуль.")

        self.balance += amount

    def __str__(self):
        return f"Рахунок власника {self._owner}: {self.balance:.2f} грн"


def task_2():
    print("\n" + "=" * 60)
    print("ЗАВДАННЯ 2. ІНКАПСУЛЯЦІЯ ТА PROPERTY")
    print("=" * 60)

    account = BankAccount("Вікторія", 5000)

    print("\nКоректно створений рахунок:")
    print(account)
    print(f"Річний бонус: {account.annual_bonus:.2f} грн")

    print("\nПеревірка зміни балансу:")
    account.deposit(1000)
    print(account)
    print(f"Новий річний бонус: {account.annual_bonus:.2f} грн")

    print("\nПеревірка некоректних значень:")

    try:
        account.balance = -500
    except ValueError as error:
        print("Помилка:", error)

    try:
        account.balance = "тисяча"
    except TypeError as error:
        print("Помилка:", error)

    print("\nСпроба прямого доступу до приватного атрибута:")
    try:
        print(account.__balance)
    except AttributeError as error:
        print("Помилка:", error)

    print("\nДоступ через механізм name mangling:")
    print("Приватний баланс:", account._BankAccount__balance)

    print("\nДоступ до захищеного атрибута:")
    print("Власник рахунку:", account._owner)


# ============================================================
# ЗАВДАННЯ 3. МАГІЧНІ МЕТОДИ ТА ПЕРЕВАНТАЖЕННЯ ОПЕРАТОРІВ
# Предметна область: грошова сума
# ============================================================

class Money:
    """Клас для роботи з грошовими сумами в одній валюті."""

    def __init__(self, amount, currency="UAH"):
        if isinstance(amount, bool) or not isinstance(amount, (int, float)):
            raise TypeError("Сума повинна бути числом.")

        self.amount = round(amount, 2)
        self.currency = currency

    def __str__(self):
        return f"{self.amount:.2f} {self.currency}"

    def __repr__(self):
        return f"Money({self.amount!r}, {self.currency!r})"

    def __add__(self, other):
        if not isinstance(other, Money):
            return NotImplemented
        if self.currency != other.currency:
            raise ValueError("Не можна додавати різні валюти.")
        return Money(self.amount + other.amount, self.currency)

    def __sub__(self, other):
        if not isinstance(other, Money):
            return NotImplemented
        if self.currency != other.currency:
            raise ValueError("Не можна віднімати різні валюти.")
        return Money(self.amount - other.amount, self.currency)

    def __mul__(self, number):
        if isinstance(number, bool) or not isinstance(number, (int, float)):
            return NotImplemented
        return Money(self.amount * number, self.currency)

    def __eq__(self, other):
        if not isinstance(other, Money):
            return NotImplemented
        return (
            self.amount == other.amount
            and self.currency == other.currency
        )

    def __lt__(self, other):
        if not isinstance(other, Money):
            return NotImplemented
        if self.currency != other.currency:
            raise ValueError("Не можна порівнювати різні валюти.")
        return self.amount < other.amount

    def __abs__(self):
        return Money(abs(self.amount), self.currency)


def task_3():
    print("\n" + "=" * 60)
    print("ЗАВДАННЯ 3. МАГІЧНІ МЕТОДИ")
    print("=" * 60)

    first = Money(1500, "UAH")
    second = Money(500, "UAH")

    print("\nПредставлення об'єктів:")
    print("str:", str(first))
    print("repr:", repr(first))

    print("\nАрифметичні операції:")
    print("Додавання:", first + second)
    print("Віднімання:", first - second)
    print("Множення:", second * 3)

    print("\nПорівняння:")
    print("Чи рівні суми:", first == second)
    print("Чи перша сума менша:", first < second)

    print("\nОперація abs():")
    negative = Money(-250, "UAH")
    print("Модуль суми:", abs(negative))


# ============================================================
# ЗАВДАННЯ 4. АБСТРАКТНІ КЛАСИ ТА КОМПОЗИЦІЯ
# Предметна область: система обробки платежів
# ============================================================

class PaymentProcessor(ABC):
    """Абстрактний базовий клас платіжного процесора."""

    @property
    @abstractmethod
    def name(self):
        pass

    @abstractmethod
    def process_payment(self, amount):
        pass

    @abstractmethod
    def refund(self, amount):
        pass

    def show_information(self):
        """Конкретний метод, спільний для підкласів."""
        return f"Платіжний метод: {self.name}"


class CardPayment(PaymentProcessor):
    @property
    def name(self):
        return "Банківська картка"

    def process_payment(self, amount):
        return f"Оплату карткою на суму {amount:.2f} грн виконано."

    def refund(self, amount):
        return f"Повернення {amount:.2f} грн на картку виконано."


class MobilePayment(PaymentProcessor):
    @property
    def name(self):
        return "Мобільний платіж"

    def process_payment(self, amount):
        return f"Мобільну оплату на суму {amount:.2f} грн виконано."

    def refund(self, amount):
        return f"Повернення {amount:.2f} грн через мобільний платіж виконано."


class PaymentLogger:
    """Компонент для журналювання платіжних операцій."""

    def log(self, message):
        print("[ЖУРНАЛ]:", message)


class PaymentOrder:
    """Замовлення, яке містить платіжний процесор і журнал."""

    def __init__(self, processor, logger):
        self.processor = processor  # Композиція
        self.logger = logger        # Композиція

    def pay(self, amount):
        result = self.processor.process_payment(amount)
        self.logger.log(result)
        return result

    def make_refund(self, amount):
        result = self.processor.refund(amount)
        self.logger.log(result)
        return result


def task_4():
    print("\n" + "=" * 60)
    print("ЗАВДАННЯ 4. АБСТРАКТНІ КЛАСИ ТА КОМПОЗИЦІЯ")
    print("=" * 60)

    print("\nСпроба створення абстрактного класу:")
    try:
        processor = PaymentProcessor()
    except TypeError as error:
        print("Помилка:", error)

    card = CardPayment()
    mobile = MobilePayment()

    print("\nІнформація про платіжні методи:")
    print(card.show_information())
    print(mobile.show_information())

    print("\nПоліморфний виклик:")
    processors = [card, mobile]

    for processor in processors:
        print(processor.process_payment(1200))
        print(processor.refund(200))

    print("\nДемонстрація композиції:")
    logger = PaymentLogger()
    order = PaymentOrder(card, logger)

    order.pay(2500)
    order.make_refund(500)


# ============================================================
# ЗАВДАННЯ 5. ПАТТЕРН ПРОЄКТУВАННЯ STRATEGY
# Предметна область: вибір способу доставки замовлення
# ============================================================

class DeliveryStrategy(ABC):
    """Спільний інтерфейс стратегій доставки."""

    @abstractmethod
    def calculate_cost(self, distance):
        pass

    @abstractmethod
    def get_name(self):
        pass


class CourierDelivery(DeliveryStrategy):
    def calculate_cost(self, distance):
        return 50 + distance * 12

    def get_name(self):
        return "Кур'єрська доставка"


class PostDelivery(DeliveryStrategy):
    def calculate_cost(self, distance):
        return 40 + distance * 5

    def get_name(self):
        return "Доставка поштою"


class PickupDelivery(DeliveryStrategy):
    def calculate_cost(self, distance):
        return 0

    def get_name(self):
        return "Самовивіз"


class ExpressDelivery(DeliveryStrategy):
    """Нова стратегія, додана без зміни наявних класів."""

    def calculate_cost(self, distance):
        return 100 + distance * 18

    def get_name(self):
        return "Експрес-доставка"


class DeliveryService:
    """Контекст, який використовує вибрану стратегію."""

    def __init__(self, strategy):
        self.strategy = strategy

    def set_strategy(self, strategy):
        self.strategy = strategy

    def show_delivery_cost(self, distance):
        cost = self.strategy.calculate_cost(distance)
        return (
            f"{self.strategy.get_name()}: "
            f"{cost:.2f} грн за {distance} км."
        )


def task_5():
    print("\n" + "=" * 60)
    print("ЗАВДАННЯ 5. ПАТТЕРН STRATEGY")
    print("=" * 60)

    service = DeliveryService(CourierDelivery())

    print("\nВибір різних способів доставки:")
    print(service.show_delivery_cost(10))

    service.set_strategy(PostDelivery())
    print(service.show_delivery_cost(10))

    service.set_strategy(PickupDelivery())
    print(service.show_delivery_cost(10))

    print("\nДодавання нової стратегії:")
    service.set_strategy(ExpressDelivery())
    print(service.show_delivery_cost(10))


# ============================================================
# ГОЛОВНА ФУНКЦІЯ ЗАПУСКУ ПРОГРАМИ
# ============================================================

def main():
    print("=" * 60)
    print("ЛАБОРАТОРНА РОБОТА №2")
    print("ОБ'ЄКТНО-ОРІЄНТОВАНЕ ПРОГРАМУВАННЯ В PYTHON")
    print("=" * 60)

    task_1()
    task_2()
    task_3()
    task_4()
    task_5()

    print("\n" + "=" * 60)
    print("УСІ П'ЯТЬ ЗАВДАНЬ ВИКОНАНО УСПІШНО!")
    print("=" * 60)


if __name__ == "__main__":
    main()

