# Заключительный коллоквиум №3

**Дата:** 15.04.2025

## Вопрос:

**Дать определение и не менее 3-х примеров использования классических поведенческих шаблонов проектирования. Рассматривать как пары «проблема/проблемы - решение» через призму инкапсуляции, универсального инженерного принципа «разделяй и властвуй» и разработки на базе ортогональных стратегий + как влияет и влияет ли многопоточное выполнение.**

---

## Ответ:

### Определение

**Поведенческие шаблоны проектирования** — это категория шаблонов, описанных в книге "Design Patterns: Elements of Reusable Object-Oriented Software" (GoF), предназначенная для управления алгоритмами, потоками управления и взаимодействием между объектами. Их основная цель — **снижение связности компонентов**, повышение гибкости, повторного использования и расширяемости системы.

Они реализуют:
- **Инкапсуляцию поведения** — выделение поведения в отдельные объекты
- Принцип **«разделяй и властвуй»** — разделение зон ответственности между модулями
- **Ортогональные стратегии** — возможность независимой замены частей системы без влияния на другие (например, алгоритмов)

---

### Пример 1: Команда (Command)

**Проблема:**
Нужно отделить объект, инициирующий действие, от объекта, выполняющего его. Это важно при реализации операций с возможностью отмены, журналирования или отложенного исполнения.

**Решение:**
Инкапсуляция действия и его параметров в объект-команду с методом `Execute()`.

- **Инкапсуляция:** объект-команда содержит всю логику действия
- **Разделяй и властвуй:** вызывающий код не знает деталей реализации
- **Ортогональные стратегии:** каждую команду можно обрабатывать независимо
- **Многопоточность:** команды могут помещаться в потокобезопасную очередь и исполняться параллельно пулом потоков

#### Компоненты:
- `Command` — интерфейс команды
- `ConcreteCommand` — конкретная реализация команды
- `Receiver` — исполнитель
- `Invoker` — объект, инициирующий выполнение

#### Пример на C++:
```cpp
class Command {
public:
    virtual void Execute() = 0;
};

class OpenFileCommand : public Command {
    std::string filename;
public:
    OpenFileCommand(const std::string& file) : filename(file) {}
    void Execute() override {
        ShellExecuteA(NULL, "open", filename.c_str(), NULL, NULL, SW_SHOW);
    }
};

class Invoker {
    Command* cmd;
public:
    void SetCommand(Command* c) { cmd = c; }
    void Run() { if (cmd) cmd->Execute(); }
};
```

---

### Пример 2: Стратегия (Strategy)

**Проблема:**
Нужно уметь подменять алгоритм (например, сортировки или логирования) в рантайме без изменения клиентского кода.

**Решение:**
Определение интерфейса стратегии и реализация различных алгоритмов как взаимозаменяемых объектов.

- **Инкапсуляция:** каждый алгоритм выделен в отдельный класс
- **Разделяй и властвуй:** выбор алгоритма отделён от его использования
- **Ортогональные стратегии:** стратегии не зависят друг от друга
- **Многопоточность:** стратегии без состояния безопасны для параллельного использования

#### Пример на C++:
```cpp
class LoggerStrategy {
public:
    virtual void Log(const std::string&) = 0;
};

class ConsoleLogger : public LoggerStrategy {
public:
    void Log(const std::string& msg) override {
        std::cout << msg << std::endl;
    }
};

class Application {
    LoggerStrategy* logger;
public:
    Application(LoggerStrategy* l) : logger(l) {}
    void Process() {
        logger->Log("Starting process...");
    }
};
```

---

### Пример 3: Шаблонный метод (Template Method)

**Проблема:**
Требуется реализовать алгоритм с общей структурой, но с возможностью переопределения отдельных шагов.

**Решение:**
Создание базового класса с реализацией общего алгоритма и виртуальных методов для переопределяемых шагов.

- **Инкапсуляция:** общая и специфическая логика разделены
- **Разделяй и властвуй:** структура алгоритма отделена от деталей
- **Ортогональные стратегии:** шаги можно подменять независимо
- **Многопоточность:** можно применять разные реализации в разных потоках

#### Пример на C++:
```cpp
class WindowHandler {
public:
    void HandleEvent() {
        PreProcess();
        Process();
        PostProcess();
    }
protected:
    virtual void PreProcess() = 0;
    virtual void Process() = 0;
    virtual void PostProcess() = 0;
};
```

---

### Объяснение: Ортогональные стратегии

Ортогональность в проектировании означает независимость компонентов. Стратегии считаются ортогональными, если:
- Они реализуют один интерфейс
- Их можно заменять без изменения остального кода
- Они не зависят от внутренней логики других стратегий

Такой подход облегчает тестирование, расширение и масштабирование системы, особенно при многопоточности.

---



---

### Архитектура программного обеспечения и многопоточность

**Архитектура программного обеспечения (ПО)** — это совокупность фундаментальных структур системы и принципов, управляющих её проектированием и развитием. Она определяет:

- разбиение системы на модули и компоненты,
- способы их взаимодействия,
- распределение ответственности между ними.

Как показано на примерах шаблонов **Command**, **Strategy** и **Template Method**, поведенческие шаблоны напрямую влияют на архитектуру, позволяя:
- выстраивать гибкие и расширяемые интерфейсы,
- минимизировать связанность между компонентами,
- реализовывать поведение как заменяемые стратегии и сценарии.

**Многопоточность** оказывает сильное влияние на архитектуру:
- Требует **изоляции состояния** между потоками
- Вводит необходимость в **синхронизацию и управление ресурсами**
- Требует **чёткой модульности**, чтобы избежать гонок данных и блокировок
- Преимущества шаблонов: шаблоны Command, Strategy, Template Method — позволяют организовать **безопасное исполнение поведения в разных потоках** за счёт слабой связанности и изолированных реализаций

Таким образом, поведенческие шаблоны являются важным инструментом архитектурного проектирования в многопоточной среде.

---



---

### Вывод:

Поведенческие шаблоны делают архитектуру гибкой и расширяемой. Они упрощают поддержку, повторное использование и тестирование компонентов. В многопоточной среде эти шаблоны позволяют масштабировать поведение без изменения основной логики, особенно при условии правильной изоляции состояния и синхронизации данных.
