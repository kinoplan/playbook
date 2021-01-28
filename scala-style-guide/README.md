# KINOPLAN 
# SCALA STYLE GUIDE
Здесь содержатся рекомендации по стилю написания кода Scala в Kinoplan. 

Цели этого руководства:

* Следовать тем правилам стиля, которые описаны здесь.
* Своевременно совершенствовать это руководство по мере изучения и развития Scala.
* Принципы стиля должны быть ясны.

**Если команда решает что-то писать не так в своем проекте, то она документирует это в CONTRIBUTING.md проекта.**

Некоторые правила могут приносить вред в меньшинстве случаев (ухудшать читаемость кода, например), но ради единообразия им все равно нужно следовать.
Однако все из них обсуждаемые.

## <a name='toc'>Оглавление</a>
1. [Синтаксический стиль](#syntactic_style)
	* [Соглашение об именовании](#naming_convention)
	* [Соглашение об именовании переменных](#variable_naming_convention)
	* [Длина строки](#linelength)
	* [Пробелы](#whitespace)
	* [Правило 30](#rule_of_30)
	* [Отступы](#indent)
	* [Вертикальные пробелы](#vertical_whitespace)
	* [Круглые скобки](#brackets)
	* [Фигурные скобки](#figure_brackets)
	* [Литералы](#literals)
	* [Импорты](#imports)
	* [Сортировка внутри класса](#class_ordering)
	* [Pattern Matching](#pattern_matching)
	* [Инфиксная нотация](#infix_notation)
	* [Анонимный метод](#anonymous_method)
2. [Основы языка](#basic_language)
   * [Case class](#case_class)
   * [Перечисления](#enum)
   * [Apply методы](#apply_method)
   * [Модификатор override](#override_modifier)
   * [Кортежи](#tuples)
   * [Работа с датами](#datetime)
   * [Вызов по имени](#call_by_name)
   * [Множественные списки параметров](#multiple_parameter_lists)
   * [Передача именованных функций](#passing_named_functions)
   * [Перегрузка операторов](#operator_overloading)
   * [Вывод типа](#type_inference)
   * [Оператор return](#return_statements)
   * [Функциональное программирование](#funcprog)
   * [Неявные преобразования (implicits)](#implicits)
3. [Обработка исключений](#exception_handling)
	* [Throw](#throw)
	* [Try vs try](#try_vs_try)
	* [Option](#option)
	* [Монады](#monadic)
4. [Параллелизм](#concurrency)
	* [concurrent.Map](#concurrent_map)
	* [Явная синхронизация и параллельные коллекции](#concurrency-sync-and-map)
	* [Аннотация volatile](#volatile_annotation)
	* [Закрытое поле](#private_field)
	* [Изоляция](#isolation)
5. [Play Framework](#play_framework)
	* [Структура проекта](#project_structure)
	* [Новые проекты](#new_projects)
	* [Контроллеры](#controllers)
	* [Модели](#models)
6. [Akka](#akka)
	* [Структура актора](#actor_structure)
	* [Ask и Tell](#ask_and_tell)
7. [Производительность](#performance)
	* [Тесты производительности](#microbenchmarks)
	* [While](#while)
	* [Option и null](#option_and_null)
	* [Библиотека коллекций Scala](#scala_collection_library)
	* [private[this]](#private_this)
8. [Тестирование](#testing)
9. [Документирование](#documentation)
	* [Комментарии](#commentary)
	* [Apidoc](#apidoc)
	* [Scaladoc](#scaladoc)
10. [Git](#git)
11. [Разное](#miscellaneous)
    * [Архитектура](#architecture)
    * [Технический долг](#tech_debt)
    * [Не изобретай колесо](#not_reinventing_the_wheel)
    * [Правило бойскаута](#boy_scout_rule)

## <a name='syntactic_style'>Синтаксический стиль</a>

### <a name='naming_convention'>Соглашение об именовании</a>
* **Классы**, **трейты**, **объекты** должны следовать стилю **UpperCamelCase**.

  ```scala
    class CategoryCinemaController
  
    case class CategoryCinema

    trait CategoryCinemaJson
  
    object CategoryCinema
  ```

* **Аббревиатуры** именуются как отдельные слова - в **UpperCamelCase** (исключение - `DAO`).
  
  ```scala
    // хорошо
    class RepertoireDAO
    
    // хорошо
    class KdmDAO
    
    // плохо
    class KDMDAO
  ```
  
* **Пакеты** - строчными буквам ASCII и **желательно (но не обязательно)** одним словом (если возникает необходимость в нескольких словах - стоит подумать о вложенных пакетах).
  
  ```scala
    package models.placement.extensions
  ```

* **Методы/функции** должны быть названы в стиле **camelCase**.

  ```scala
    def cinemaCategories
  ```

* **Константы** должны следовать стилю **UpperSnakeCase** и быть помещены в объект **companion**.
  
  ```scala
    object Region {
      val DEFAULT_TIME_ZONE = 3
    }
  ```

* **Перечисления** должны быть в **UpperCamelCase**.

  ```scala
  sealed abstract class PaymentMethod ...
   
  object PaymentMethod ... {
    case object Advance extends PaymentMethod
    case object FullPayment extends PaymentMethod
    ...
  }
  ```

* Аннотации должны быть в **UpperCamelCase**.
  
  ```scala
    @SuppressWarnings
  ```

* Поля `json` и поля в базе данных должны быть в  **SnakeCase**.

  ```scala
  {
	 "cinema_id": 1803,
	 "is_on_sale": true
  }
  ```

* Поля `headers`должны быть через `-`.

	```scala
	X-Device-Token
	```
 
* Сокращение слов допустимо только в самых общепринятых случаях, в остальных писать полностью. Например `DAO`, но не `cfg` и `conn`, вместо этого `config` и `connection`.

* В рамках одного файла старайтесь придерживаться одного стиля в использовании конструкций, например, 
не стоит писать Try recover (без возвращаемого значения) и try catch вместе.

### <a name='variable_naming_convention'>Соглашение об именовании переменных</a>
* **Переменные** должны быть названы в стиле **camelCase** и должны иметь "говорящие" имена.

  ```scala
    val serverPort = 3000
  
    val clientPort = 9000
  ```
  
* В названии переменной не должно быть тавтологии, название переменной должно быть читабельным.
  
  ```scala
    val releases = ...    // хорошо
  
    val releaseList = ... // плохо
  ```
  ```scala
    val seance = ...    // хорошо
  
    val s = ... // плохо
  ```
  ```scala
    val user = ...    // хорошо
  
    val userModel = ... // плохо
  ```
  
* Разрешается использование односимвольных имен переменных в небольшой локализованной области. Например, `i` используется в качестве индекса цикла для небольшого тела цикла.
* Для значений типа `Option[_]` в конце имени добавляется `O`. Это также верно для атрибутов/методов модели, но неверно для атрибутов/методов всех остальных классов (сервисы, контроллеры и др.)
	
  ```scala
  def list(releaseIdO: Option[Int]) = ...
  ```
  ```scala
  case class UserProfile(
    addressO: Option[Address]
  ) {
    def streetO: Option[String] = addressO.map(_.street)
  }
  ```
  ```scala
  class UserService {
    def authUser: Option[User] = ...
  }
  ```

* Для значений типа `Future[_]` в конце имени добавляется `F`.
	
	```scala
	val releaseF: Future[Release] = ...
	```
  
### <a name='linelength'>Длина строки</a>
* Используйте максимальную длину строки равную **120 символов**.
* Исключениями могут являться операторы импорта, URL-адреса, routes, различные конфиги.

### <a name='whitespace'>Пробелы</a>
* Используйте **два** пробела для отступа.
* Добавляйте **новую строку** в конец каждого файла.

  ```scala
  class C {
    def f: Int = 1
    def g: String = "s"
  }
  ```
* Отступы для Play Json комбинаторов и им подобным.

  ```scala
  // плохо, хотя IDE может так форматировать
  val onlyRuEnReads: Reads[Title] = (
    (__ \ "ru").readWithDefault("") and
    (__ \ "en").readWithDefault("")
    ) (Title.apply(_, _))
  ```
  ```scala
  // хорошо
  val onlyRuEnReads: Reads[Title] = (
    (__ \ "ru").readWithDefault("") and
    (__ \ "en").readWithDefault("")
  ) (Title.apply(_, _))
  ```

### <a name='rule_of_30'>Правило 30</a>
Если элемент состоит из более чем 30 подэлементов, весьма вероятно, что существует серьезная проблема архитектуры. Поэтому, старайтесь придерживаться следующих рекомендаций:
	
* Старайтесь писать метод так, чтобы он содержал **менее 30 строк кода**.
* Старайтесь писать класс так, чтобы он содержал **менее 30 методов**.
* Исключениями могут быть самописные сложные алгоритмы, однако и в этом случае рекомендуется не пренебрегать этим правилом.

### <a name='indent'>Отступы</a>
* **Один пробел** до и после операторов.

  ```scala
    def mul(a: Int, b: Int) = a * b // хорошо
  
    def mul(a: Int, b: Int) = a*b   // плохо
  ```
  
* **Один пробел** после запятой.

  ```scala
    List(1, 2, 3) // хорошо
  
    List(1,2,3)   // плохо
  ```
  
* **Один пробел** до и после двоеточия для `type bound`.

	```scala
	// хорошо
	def doSomething[M[_] : Monoid : Writes](m: M[Int]) = ...
	
	// плохо
	def doSomething[M[_]: Monoid: Writes](m: M[Int]) = ...
	```
	
* **Один пробел** после двоеточия.

  ```scala
    def mul(a: Int, b: Int): Int = a * b // хорошо
  
    //не ставь пробелы перед двоеточиями, кроме type bound
    def mul(a : Int, b : Int) : Int = a * b // плохо
    
    //не пропускай пробелы после двоеточия
    def mul(a:Int, b:Int):Int = a * b // плохо
  ```
  
* **Два пробела** для отсупов.

  ```scala
    if (smth) {
      println("Smth!") // хорошо
    }
    
    if (smth) {
        println("Smth!") // плохо
    }
  ```
  
* Для объявления метода используйте **два пробела** для отступа. Если параметры и возращаемый тип не помещаются в одну строку, то поместите каждый параметр в новую строку, при этом возвращаемый тип должен быть с новой строки.

  ```scala
  	// хорошо
    def planning(start: DateTime, isManual: Boolean): Boolean = {
    
   // плохо
   def planning(start: DateTime, isManual: Boolean)
   : Boolean = {
   
   // плохо
   def planning(start: DateTime, 
   isManual: Boolean): Boolean = {
  ```
  
  ```scala
    // хорошо
    def allocating(
      placements: List[Placement],
      seances: List[Seance],
      twisterHelp: TwisterHelp,
      userIds: Set[String]
    ): (TwisterHelp, List[PlacementApart]) = {
    
    // плохо
    def allocating(
      placements: List[Placement],
      seances: List[Seance],
      twisterHelp: TwisterHelp,
      userIds: Set[String])
    :(TwisterHelp, List[PlacementApart]) = {
  ```

* Для классов, заголовок которых не помещается в одну строку, используйте **два пробела** для отступа его параметров, затем поместите закрывающую скобку и расширение на следующую строку.

  ```scala
    // хорошо
    class SeanceController @Inject()(
      authUtils: AuthUtils,
      authFilters: AuthFilters,
      seanceService: SeanceService,
      releaseService: ReleaseService
    ) extends InjectedController with Smth {
    
    // плохо
    class SeanceController @Inject()(
      authUtils: AuthUtils,
      authFilters: AuthFilters,
      seanceService: SeanceService,
      releaseService: ReleaseService)
     extends InjectedController 
     with Smth 
     {
  ```
  
* Для вызова метода, класса если не помещается в одну строку, то используйте **два пробела** для отсупа его параметров и поместите каждый параметр в новую строку.

  ```scala
    // хорошо
    allocating(
      placements,
      suitableSeances,
      twisterHelp,
      userIds
    )
    
    // плохо
    allocating(placements,
      suitableSeances,
      twisterHelp,
      userIds)
  ```
  ```scala
    // хорошо
    val result = allocating(
      placements,
      suitableSeances,
      twisterHelp,
      userIds
    )
    
    // плохо
    val result = allocating(placements,
                            suitableSeances,
                            twisterHelp,
                            userIds)
  ```
  ```scala
    // хорошо
    new Info(
      placements,
      suitableSeances,
      twisterHelp,
      userIds
    )
    
    // плохо
    new Info(
      placements,
      suitableSeances,
      twisterHelp,
      userIds)

  ```

* Не используйте выравнивание по вертикали. Это затрудняет изменение выровненного кода в будущем. Исключения могут соствлять не `*.scala` файлы: routes, sbt файлы, конфиги и прочее.

  ```scala
  // хорошо
  val MIN_QUOTA = 0
  val MAX_QUOTA = 100
  val DEFAULT_QUOTA = 50
  
  // плохо
  val MIN_QUOTA     = 0
  val MAX_QUOTA     = 100
  val DEFAULT_QUOTA = 50
  ```

### <a name='vertical_whitespace'>Вертикальные пробелы (пустые строки)</a>
* **Один вертикальный пробел**
  * Между последовательными членами класса: поля, конструкторы, методы, вложенные классы и т.д.
  * Внутри тела метода, для создания логических групп операторов.
      
  ```scala
  // хорошо
  val seances = ...
  
  val releases = ...
  
  
  // тоже хорошо (логическая группа полей)
  val releaseIds = ...
  val releases = ...
  
  // хорошо, так как похожи по смыслу и после равно идет однострочный код
  val seances = seanceService.find(...)
  val releases = releaseService.find(...)
  val cinemas = cinemaService.find(...)
  
  // плохо, так как после равно идет многострочный код
  val seances = {
  ...
  }
  val releases = {
  ...
  }
  ```
  
* Используйте **один вертикальный пробел** для разделения определений классов или объектов.
* Не допускайте **больше одного** вертикального пробела между элементами.
* Не злоупотребляйте вертикальными пробелами.

### <a name='brackets'>Круглые скобки</a>
* Используйте круглые скобки в функциях без параметров **во всех случаях** (в том числе и для чистых функций).
  
### <a name='figure_brackets'>Фигурные скобки</a>
* Ставьте фигурные скобки даже вокруг однострочных условных или петлевых операторов. Исключение - использование `if/else` как однострочный тернарный оператор.

  ```scala
  // хорошо
  if (smth) {
    println("Smth!")
  }
  
  // хорошо
  if (smth) first else second
  
  // хорошо
  try {
    smth()
  } catch {
    ...
  }
  
  // плохо
  if (smth) 
    println("Smth!")
    
  // плохо
  try smth() catch {
    ...
  }
  ```
* Допустимо не ставить фигурные скобки, если тело метода целиком находится на той же строке.

  ```scala
  def f: Int = calcSomeNumber
  ```
  
  Но:
  ```scala
  def f: Int = {
    calcSomeNumber
  }
  ```
* Другое исключение - когда единственный вызов метода обрамляет всю остальную часть кода.

  ```scala
  def writes(advertisingFeatureO: Option[String]): Writes[SeanceProperty] = o => Json.obj(
    "advertising_feature" -> advertisingFeatureO,
    "is_auto_cancel_disabled" -> o.isAutoCancelDisabled
  )
  ```
  ```scala
  def insert(value: T): Future[WriteResult] = collection.flatMap {
    _.insert(ordered = false).one(value)
  }
  ```

### <a name='literals'>Литералы</a>
* **Всегда используйте прописные литералы,** вместо строчных. Строчные литералы труднее дифференцировать, особенно это заметно для типа `Long` - `l` и `1`.

  ```scala
   val longNumber = 1L // хорошо
   
   val floatNumber = 1F // хорошо
   
   val longNumber = 1l // плохо
   
   val floatNumber = 1f // плохо
  ```

### <a name='imports'>Импорты</a>
* Большинство `import` в файле должны располагаться вверху, прямо под названием пакета. Единственное случай, когда вы можете это нарушить, это импорт некоторых или всех имен из объекта внутрь класса или метода (например, довольно удобно импортить `request` внутри метода).
	
	```scala
	def find(id: ObjectId) = (
     authUtils.authenticateAction() andThen
     authFilters.accessAdvertising andThen
     authUtils.campaignAction(id)
   ) { request =>
     import request.{campaign, user}
     
     placementService.find(campaign, user) match {
       ...
     }
   }
	```
	
* Не используйте в импорте оператор `_`, если вы не импортируете более 4 сущностей. При таком импорте код становится менее устойчивым к внешним воздействиям, происходит лишнее загромождение пространоства имен, увеличивается риск появления конфликта имен. Более подробно можно почитать [тут](https://stackoverflow.com/questions/147454/why-is-using-a-wild-card-with-a-java-import-statement-bad).
* Для большинства `import` используйте полный путь (например, `scala.util.Random`) вместо относительного (например, `util.Random`). Однако, для решения конфликта гораздо удобнее использовать относительный путь (например, `mutable.Map` и `Map`, `ucs.Cinema` и `kinoplan.Cinema` и т.д.).
* Импорт сортируется в следущем порядке:
   * `java.*` и  `javax.*`
   * `scala.*`
   * `akka.*`
   * Сторонние библиотеки (`org.*`, `com.*` и т.д.).
* Конфигурация стиля кода InteliJ позволяет автоматически группировать импорт по пространству имен. Используйте шаблон import layout в Intelij, для этого зайдите в настройки `Settings->Editor->Code Style->Scala`, затем выберете вкладку Imports и приведите import layout к следующему виду:

	```scala
	java
	javax
	_______ blank line _______
	scala
	_______ blank line _______
	akka
	all other imports
	```
* Вы также должны регулярно запускать Intellij'S Optimize Imports (`Code->Optimize Imports`) перед созданием пулреквеста, для поддержания чистоты `import`.

### <a name='class_ordering'>Сортировка внутри класса</a>
Сортировка внутри класса должна идти по порядку по следующему шаблону: 	
	
* импорты внутри класса(если таковые имеются)
* поля класса
* методы
  * методы поиска
  * методы создания
  * методы обновления и/или изменения
  * методы удаления
   
   ```scala
	class CampaignController {
	  // сначала импорты, если необходимо
	  import Helper._
	  ...
	  
	  // потом переменные
	  val PORT = 3000
	  ...
	  
	  // затем методы
	  
	  // методы поиска
	  def all = ...
	  ...
	  
	  // методы создания
	  def create() = ...
	  ...

     // методы обновления и/или изменения
	  def update() = ...
	  
	  def copy() = ...
	  ...
	    
	  // методы удаления
	  def delete = ...
	  ...
	}
   ```

### <a name='pattern_matching'>Pattern Matching</a>
* Для метода, все тело которого является выражением, поместите match на ту же строку что и объявление метода.

   ```scala
	def title(status: String) = status match {
	...
	}
	```
	
* Если при вызове функции существует только один case, то поместите его в ту же строку, что и вызов функции. Если же вызов case не помещается в ту же строку, то перенесите его на следующую строку.

   ```scala
   // хорошо
	statuses.zipWithIndex.map { case (status, index) =>
	...
	}
	
	// хорошо
	tuple.zipWithIndex.map { 
	  case ((status, placement, campaign, calculation), index) =>
	  ...
	}
	
	// плохо
	tuple.zipWithIndex.map { 
	  case ((status, placement, campaign, calculation), index) => {
	  ...
	  }
	}
	```
	
* Если при вызове функции существует несколько case, то поместите каждый case на новую строку.
  
   ```scala
	list.map { 
	  case a: User => ...
	  case b: Hall => ...
	}
	```
	
* Пишите `case` **без фигурных скобок**.	
* Если `case` соответствует типу объекта, то не разворачивайте его аргументы, так как это делает рефакторинг более трудным и код более подвержен ошибкам.

   ```scala
   case class User(id: Int, title: String)
   case class Cinema(id: Int, title: String)
   
	// плохо потому, что
	// 1. При добавление новых полей нужно их добавлять и в шаблоне
	// 2. Легко словить несовпадение элементов 
	lists.map { 
	  case list @ User(id, _) => ...
	  case list @ Cinema(_, title) => ...
	}
	
	// хорошо
	lists.map { 
	  case list: User => ...
	  case list: Cinema => ...
	}
	```

### <a name='infix_notation'>Инфиксная нотация</a>
**Избегайте инфиксной нотации** для методов, которые не являются символическими методами.

```scala
// хорошо
releases.map(...)
status.contains("active")
   
// плохо
releases map (...)
status contains "active"

// но перегруженные операторы должны быть вызваны в инфиксном стиле
a mod b
a += 1
```
	
### <a name='anonymous_method'>Анонимный метод</a>
**Избегайте чрезмерного использования скобок и фигурных скобок** для анонимных методов.

```scala
// хорошо
releases.map { release =>
  ...
}

// хорошо
releases.map(release => ...)

// плохо
releases.map(release => {
  ...
})

// плохо
releases.map { release => {
  ...
}}

// плохо
releases.map({ release => ... })
```


## <a name='basic_language'>Основы языка</a>

### <a name='case_class'>Case class</a>
* Параметры конструктора не должны быть изменяемыми для case class. Вместо этого используйте конструктор копирования. Наличие изменяемых классов через `var` может быть подвержено ошибкам, например, хэш-карты могут поместить объект в неправильный сегмент, используя старый хэш-код или вы присваиваете значение из `immutable` в `mutable` переменную, а затем изменяете значение `mutable` переменной, то значение `immutable` переменной также меняется.

	```scala
	// хорошо
	case class Release(id: Int, title: String, age: Int)
	
	// плохо
	case class Release(id: Int, title: String, var age: Int)
	
	// Для изменения значений, использовать копирующий конструктор для создания нового экземпляра
	val release = Release(1, "Человек, который изменил всё", 12)
	
	val updatedRelease = release.copy(age = 16)
	```
	
* В очень исключительных случаях допускается наличие у класса параметра `var`, но этот класс определенно должен быть вспомогательным и не являться моделью для коллекции из бд. И разумеется, вы должны обладать достаточными компетенциями, чтобы это использовать.

### <a name='enum'>Перечисления</a>
* Не используйте Scala перечисления, вместо этого отдавайте предпочтение case-классам, 
либо библиотеке [Enumeratum](https://github.com/lloydmeta/enumeratum), если нужно связать значения с JSON, БД и прочим.

### <a name='apply_method'>Apply методы</a>
* Используйте метод `apply` для `case class` в тех случаях, когда вы хотите создать модель через кастомный список параметров. Такой метод должен обязательно находится в объекте-компаньоне этого класса и иметь в качестве возвращаемого типа название класса.

	```scala
	case class Period(start: DateTime, end: DateTime)
	
	object Period {
	  // хорошо
	  def apply(start: DateTime): Period = ...
		
	  // плохо
	  def apply(start: DateTime): String = ...
	}
	```
* Во всех остальных случаях избегайте использования метода `apply`.

### <a name='override_modifier'>Модификатор override</a>
* Не добавляйте модификатор `override`, если это действительно не требуется, как для переопределения конкретных методов, так и для реализации абстрактных методов, так как Scala компилятор в большинстве случаев не трубует явного указывания модификатора `override`. Например:

	```scala
	// так не надо делать
	override def writes(release: Release): JsValue
	
	// однако так нужно
	override def equals(apart: Any): Boolean = {
	```
	
### <a name='tuples'>Кортежи</a>
* Используйте кортежи для извлечения нескольких переменных из одного выражения.

	```scala
	val (a, b) = (0, 1)
	```
* Однако, не используйте кортежи в конструкторе класса, особенно когды вы помечаете поле как `@transient`, так как компилятор Scala создает дополнительное поле `Tuple2` которое не будет `@transient` для примера выше.

	```scala
	class Example {
	  // не сработает, так как компилятор сгенерит non-transient Tuple2
	  @transient private val (a, b) = someTuple2()
	}
	val (a, b) = (0, 1)
	```
* Используйте pattern matching для доступа к элементам кортежа вместо доступа по номерам.
    ```scala
    // плохо
    names.map(_._1)
  
    // хорошо
    names.map { case (firstName, _) => firstName }
    ``` 
  
### <a name='datetime'>Работа с датами</a>

* Всегда используйте `Joda DateTime` для работы с датами, даже если вам нужда только часть полной даты (вместо `LocalDate`, `LocalTime`, `LocalDateTime` и др.).

    Сейчас уже написано много кода, большая часть на `DateTime`, а слишком большой выгоды от строгого разделения нет. 
    
### <a name='call_by_name'>Вызов по имени</a>
* В случаях, когда парметр может не понадобиться при вызове метода, используйте вызов по имени.
	
	```scala
	def error(message: => String): Unit = ...
	```
	
* Однако не забывайте о [побочном эффекте](https://stackoverflow.com/questions/13337338/call-by-name-vs-call-by-value-in-scala-clarification-needed), который может нести в себе вызов по имени.

### <a name='multiple_parameter_lists'>Множественные списки параметров</a>
Используйте множественные списки параметров, если это сделает ваш код более элегантным. Порой бывает удобно делать последний явный список с одной лямбдой (например, для написания `if/else` в функциональном стиле).
	
```scala
def applyIf(predicate: A => Boolean)(action: A => A): A = {
  if (predicate(value)) action(value) else value
}

val updatedSale = sale.applyIf(_.status == Created) {
  _.copy(status = Approved)
}
```

### <a name='passing_named_functions'>Передача именованных функций</a>
* Если внутри функции используется лишь одно поле, то используется вызов, через `_` и круглые скобки: 
	
	```scala
	cinema.filter(_.id == cinemaId)
	```

* Если обратный вызов принимающей функции и передаваемая функция имеют одинаковую сигнатуру, то аргументы и подчеркивания должны быть опущены:

	```scala
	// плохо
	Option(123).map(println(_))
	
	// хорошо
	Option(123).map(println)
	```


### <a name='operator_overloading'>Перегрузка операторов</a>
**Используйте имена символических методов**, если они не затрудняют понимание намерений методов. В противном случае, не используйте их.

```scala
// хорошо, говорящее именование
JsObject.empty ?+ ("comment" -> commentO)

// плохо
JsObject.empty.addIfDefined("comment" -> commentO)

// плохо, трудно понять
stream1 >>= stream2

// хорошо, очевидно что происходит
stream1.join(stream2)
```

### <a name='type_inference'>Вывод типа</a>
* Все публичные и защищенные (`protected`) методы и атрибуты должны иметь явную типизацию.
* **Методы или переменные с неочевидным типом должны быть явно типизированы.** Хорошим тестом является то, что если ревьювер не может определить тип за 5 секунд, то следует использовать явные типы.


### <a name='return_statements'>Оператор return</a>
**Избегайте использования оператора return.** Компилятор Scala преобразует его в `try/catch`, что может привести к неожиданному поведению.


### <a name='funcprog'>Функциональное программирование</a>
* Для методов, предназначенных для рекурсивной обработки, старайтесь по возможности применять аннотацию `@tailrec`, если не происходят асихронные операции в рекусрии. Во-первых, тем самым компилятор Scala проверяет метод на рекурсивность(например, что не используются замыкания, функциональные преобразования и т.д.). Во-вторых, компилятор оптимизирует рекурсию. 

* **Не используйте** императивный стиль (циклы, var, mutable структуры данных и т. д.), если это не обусловлено соображениями производительности.

### <a name='implicits'>Неявные преобразования (implicits)</a>
Используйте неявные преобразования, **только если**:

* вы создаете доменный язык.
* вы используете его для неявных параметров типа (например, `ClassTag`, `TypeTag`).
* вы используете его для своего собственного класса, чтобы уменьшить многословность преобразования из одного типа в другой.
* вы используете сериализаторы/десериализаторы моделей (тайпклассы) (`play-json`, `BSON readers & writers` и т. п.) 
  **и все поля модели отображаются в (де-)сериализуемую структуру как есть**.
  ```
  trait TitleJson {
    // уместно имплицитное преобразование
    implicit val writes: Writes[Title] = Json.writes
    
    // можно передавать только эксплицитно, т. к. есть не все поля
    val onlyEnRuWrites: Writes[Title] = (o: Title) => Json.obj(
      "en" -> o.en,
      "ru" -> o.ru
    )
    
    // то же
    val onlyRuEnReads: Reads[Title] = (
      (__ \ "ru").readWithDefault("") and
      (__ \ "en").readWithDefault("")
    ) (Title.apply(_, _))
  }
  ```
* вы пишете хелпер, обогащающий какой-либо класс стандартной библиотеки (список, число, строка и др.)
    ```scala
    object Helpers {
      implicit class ListExtended[T](val items: List[T]) extends AnyVal {
        def groupById[I](getId: T => I): Map[I, T] = {
          items.map(item => getId(item) -> item).toMap
        }
      }
    }
    ``` 

Если вы используете неявные преобразования, то вы должны убедиться, что другой программист может понять семантику использования, не читая само неявное определение. Неявные преобразования имеют довольно сложные правила и при чрезмерном использовании могут сделать кодовую базу трудной для понимания.

Если вы хотите использовать неявное преобразование, всегда спрашивайте себя, есть ли способ достичь того же самого без их помощи.

## <a name='exception_handling'>Обработка исключений</a>

### <a name='throw'>Throw</a>
* Не генерируйте исключения с помощью `throw`, аннотаций `@throw` и т.д.
* Не используйте методы, которые выбрасывают исключения.

	```scala
	// плохо, выбрасывает исключение
	require(limit > 0)
	
	// хорошо
	if (limit > 0) {
	  ...
	} else {
	  ...
	}
	```
  Это верно даже когда метод, бросающий исключение, гарантированно **не** сработает:
    ```scala
    // нельзя
    list.groupBy(_.id).mapValues(_.head) 
  
    // можно
    list.groupBy(_.id)
      .flatMap { case (id, list) => list.headOption.map((id, _)) }
    ```
* Не используйте `head` и `get` для option и коллекций. Вместо этого используйте `headOption` и раскрытие через `map/flatMap/etc.`


### <a name='try_vs_try'>Try vs try</a>
Scala предоставляет монадическую обработку ошибок (через `Try`, `Success` и `Failure`), что облегчает цепочки действий. Используйте их, если вам ясна семантика ожидаемых ошибок и исключений. В противном случае предпочтительнее использовать `try/catch`.

Старайтесь не использовать `Try` в качестве возвращаемого типа для метода или переменной.

```scala
// не рекомендуется
def get(releaseId: Int): Try[Release] = ...

// хорошо
def get(releaseId: Int): Either[String, Release] = ...
```

### <a name='option'>Option</a>
* Используйте `Option` когда значение может быть пустым. По сравнению с `null`, у `Option` явно указано в API что занчение может быть `None`.
* При построении `Option` используйте `Some` для переменных, которые не могут вернуть `null`.
	
	```scala
	def get(release: Release): Option[Release] = Some(release)
	```
* Если же при построении `Option` используется преобразование, которое может вернуть `null`, то используйте `Option`.
	
	```scala
	// хорошо, вернет None если transform вернет null
	def get1(release: Release): Option[Release] = Option(transform(release))
	
	// плохо, вернет Some(null) если transform вернет null
	def get2(release: Release): Option[Release] = Some(transform(release))
	```

* Не используйте `get` для `Option`.

### <a name='monadic'>Монады</a>
Одной из интересных особенностей Scala являются монады. Почти все (например, коллекции, `Option`, `Future`, `Try`) является монадой, и операции на них могут быть сцеплены вместе. Это невероятно мощная концепция, но сцепление следует использовать экономно. В частности:

* Избегайте сцепления и/или вложенности **более 3 операций**.
* Если в цепочке **более 2 операций**, то старайтесь использовать `for`.
* Не используйте `for` **для 1 операции**, если то же самое можно сделать через `map/flatMap/etc.`
* Старайтесь минимизировать число вложенных `map`/`flatMap`/`foreach`/`for`, для этого используйте один из инструментов для работы с nested effects (например, в cats это `OptionT`, `EitherT` и др.)

Цепочку часто можно сделать более понятной, дав промежуточному результату имя переменной, явно введя переменную и разбив ее на более процедурный стиль.

## <a name='concurrency'>Параллелизм</a>

* Нельзя использовать `Await` в конечной версии кода, **но можно в ситуациях перехода** с блокирующего API на неблокирующий. Например, есть устаревшая библиотека `Salat` и новая `Reactive Mongo`, и мы хотим постепенно переходить на вторую. 
* Если используете блокирующие библиотеки, то проверьте, необходимо ли для них настроить отдельных пул потоков. 
Это верно для [Play](https://www.playframework.com/documentation/2.8.x/AccessingAnSQLDatabase#Using-a-CustomExecutionContext) 
и [Akka](https://doc.akka.io/docs/akka-http/current/handling-blocking-operations-in-akka-http-routes.html),
вероятно, верно и для всех асинхронных фреймворков.

    Про асинхронные веб-серверы доступно [здесь](https://youtu.be/1Tp5TV3BVWE?list=PLrCZzMib1e9qZwq95WVmGB-acnot5ka4a&t=3404).
* Для цепочек `Future` всегда следите за тем, могут ли они выполняться параллельно. 
До выполнения предыдущего `Future` в `for` следующий несозданный Future [не будет запущен](https://stackoverflow.com/questions/43296040/why-arent-my-scala-futures-executing-in-parallel), 
из-за чего может увеличиться время выполнения.

    ```scala
    // будет работать последовательно
    val flatRes: Future[List[Int]] = for {
      scalaFooRes <- scalaFoo 
      scalaBarRes <- scalaBar
      scalaBazRes <- scalaBaz
    } yield (scalaFooRes ++ scalaBarRes ++ scalaBazRes)
  
    // параллельно
    val foo = scalaFoo
    val bar = scalaBar
    val baz = scalaBaz
    for {
      scalaFooRes <- foo 
      scalaBarRes <- bar
      scalaBazRes <- baz
    } ...
    ```

### <a name='concurrent_map'>concurrent.Map</a>
* Предпочтительно использовать `scala.collection.concurrent.Map` вместо `java.util.concurrent.ConcurrentHashMap`
* Однако, если версия scala меньше 2.11.6 или ваши проекты как-то взаимосвязаны с более ранними версиями scala, то следует использовать `java.util.concurrent.ConcurrentHashMap`. Это связано с тем, что метод `getOrElseUpdate` в `scala.collection.concurrent.Map` не являлся атомарной и был исправлен только в версии Scala 2.11.6 [SI-7943](https://issues.scala-lang.org/browse/SI-7943)

### <a name='concurrency-sync-and-map'>Явная синхронизация и параллельные коллекции</a>
Существует 3 рекомендуемых способа обеспечения одновременного доступа к общим состояниям. **Не смешивайте их,** потому что это может сделать программу очень трудной для понимания и привести к взаимоблокировкам.

1. `scala.collection.concurrent.Map`: Используйте, когда все состояния захвачены на карте, и ожидается высокая степень конкуренции.
	
	```scala
	private[this] val currentMap = new scala.collection.concurrent.Map[String, String]
	```
2. `java.util.Collections.synchronizedMap`: Используйте, когда все состояния захвачены на карте, и конкуренция не ожидается, но вы все равно хотите сделать код безопасным. В случае отсутствия конкуренции JVM JIT-компилятор может удалить служебные данные синхронизации путем смещенной блокировки.

	```scala
	private[this] val currentMap = java.util.Collections.synchronizedMap(new java.util.HashMap[String, String])
	```
3. Явная синхронизация путем синхронизации всех критических разделов: может использоваться для защиты нескольких переменных. Подобно предыдущему пункту, JVM JIT-компилятор может удалить служебные данные синхронизации путем смещенной блокировки.

	```scala
	class Manager {
	  private[this] var count = 0
	  private[this] val map = new java.util.HashMap[String, String]
	  def update(key: String, value: String): Unit = synchronized {
	    map.put(key, value)
	    count += 1
	  }
	  def getCount: Int = synchronized { count }
	}
	```

Для вариантов 1 и 2 не позволяйте представлениям или итераторам коллекций выходить за пределы защищенной области. Это может произойти в неочевидными способами, например, при возврате `Map.keySet` или `Map.values`. Если представления или значения необходимы для передачи, создайте копию данных.

```scala
val map = java.util.Collections.synchronizedMap(new java.util.HashMap[String, String])

// Это не сработает!
def values: Iterable[String] = map.values

// Вместо этого скопируйте элементы
def values: Iterable[String] = map.synchronized { Seq(map.values: _*) }
```

### <a name='volatile_annotation'>Аннотация volatile</a>
Пакет `java.util.concurrent.atomic` предоставляет примитивы для свободного доступа к примитивным типам, таким как `AtomicBoolean`, `AtomicInteger` и `AtomicReference`.

Всегда предпочитайте Atomic переменные в `@volatile`. Они имеют строгий набор функциональных возможностей и более заметны в коде. Atomic переменные реализуются с использованием `@volatile` под капотом.

Предпочитайте Atomic переменные при явной синхронизации, когда: 

* Все критические обновления для объекта ограничены одной переменной и ожидается конкуренция. Атомные переменные блокируются и позволяют более эффективно конкурировать.
* Синхронизация четко выражается как операция `getAndSet`.

```scala
// хорошо: четко и эффективно выражается только однократное выполнение параллельного кода
val initialized = new AtomicBoolean(false)
...
if (!initialized.getAndSet(true)) {
  ...
}

// плохо: менее понятно, что охраняется синхронизацией, может излишне синхронизировать
val initialized = false
...
var wasInitialized = false
synchronized {
  wasInitialized = initialized
  initialized = true
}
if (!wasInitialized) {
  ...
}
```

### <a name='private_field'>Закрытое поле</a>
Стоит обратить внимание, что `private` поля по-прежнему доступны для других экземпляров одного и того же класса, поэтому защита его с помощью `this.synchronized` (или просто `synchronized`) технически недостаточна. Вместо этого сделайте поле `private[this]`.

```scala
// не безопасно
class Example {
  private var count: Int = 0
  def inc(): Unit = synchronized { count += 1 }
}

// безопасно
class Example {
  private[this] var count: Int = 0
  def inc(): Unit = synchronized { count += 1 }
}
```

### <a name='isolation'>Изоляция</a>
В целом, логика параллелизма и синхронизации должна быть изолирована настолько, насколько это возможно. Это фактически означает:

* Избегайте раскрытия внутренних компонентов примитивов синхронизации в API, методах, обращенных к пользователю, и обратных вызовах.
* Для сложных модулей создайте небольшой внутренний модуль, который будет захватывать примитивы параллелизма.



## <a name='play_framework'>Play Framework</a>

* Необходимо помечать как `@Singleton` сервисы, DAO и акторы. Для контроллеров это не нужно, Play работает с ними сам.

### <a name='project_structure'>Структура проекта</a>
* **app/** - искодный код
	* **actions/** - используемые action для контроллеров
	* **actors/** - используемые акторы
	* **controllers/** - методы апи
	* **daos/** - методы доступа к базе
	* **filters/** - кастомные фильтры, логи
	* **models/** - модели, бизнес логика
	* **modules/** - подключаемые модули
	* **errors/** - кастомные ошибки
	* **services/** - прослойка между контроллерами и дао, а также сервисы, у которых нет дао (например, запросы к внешнему модулю, сервисы актора и т.д.)
	* **templates/** - twirl html
	* **utils/** - хелперы, константы, утилиты
* **conf/**
	* **application.conf** - конфигурационный файл для боевого окружения
	* **application-local.conf** - конфигурационный файл для локального окружения
	* **logback.xml** - правила логирования для боевого окружения
	* **logback-local.xml** - правила логирования для локального окружения
	* **routes** - связь апи адресов с методами контроллеров
* **lib/** - библиотеки, если таковые используются. В противном случае не создавать.
* **project/** - конфиги
	* **build.properties** - версия sbt
	* **plugins.sbt** - подключаемые плагины
* **public/** - статика(картинки и т.д.), если таковые используются. В противном случае не создавать.
* **test/** - код тестов
* **.gitignore** - файлы и папки, которые не увидит git
* **apidoc.json** - конфиг для apidoc, если такой используются. В противном случае не создавать.
* **build.sbt** - инструмент для сборки, используем только sbt
* **README.md** - описание проекта

### <a name='new_projects'>Новые проекты</a>
* Нужно генерировать новый `http.secret.key` (а не копировать из другого проекта) при помощи команды `playGenerateSecret` в `sbt`.

### <a name='controllers'>Контроллеры</a>
* Если вы используете кастомные ошибки, то старайтесь размещать их в директории `errors/` вашего проекта, при этом логически разделяя их по папкам и классам (например: внешние и внутринние ошибки в папку `external` и `internal` соответсвенно; ошибки клиента и сервиса в `ClientErrors.scala` и `ServiceErrors.scala` соответственно и т.д.).
* Внутри `Result` не должно быть дополнительных преобразований, фильтрации и прочего.
	
	```scala
	// плохо
	Ok(
	  cinemas.map { cinema =>
		cinema.filter(_.isFondKino)....
		  ....map(Json.toJson)
	  }
	)
	
	// хорошо
	Ok(Json.toJson(cinemas))
	```
* В случае, если все выполнилось правильно, но отправить нечего, то отправляем `NoContent`, не `Ok` и уж тем более не `Ok(Json.toJson("status" -> "ok"))`!

### <a name='models'>Модели</a>
* В моделях не должно быть использования сервисов. 
* На каждую модель отдельный файл. 
* В одном файле модели может находиться `case class`, `object` и `trait` для `Writes/Reads`, `Headers` и `DSL`.
* Для `Writes` всегда обозначайте принимаемую модель буквой `o`. Благодаря этому, при создании модели более удобно копипастить структуру из существующей модели. К тому же, метод находится в модели и применяется для нее же, поэтому нет смысла указывать полное имя.
	
	```scala
	// плохо
	def writes(agency: Agency) = ...
	
	// хорошо
	def writes(o: Agency) = ...
	```
	
* `Writes` разрешается создавать как в нотации функции, так и в стандартной нотации. Однако старайтесь в рамках одного проекта придерживаться конкретной нотации. 
    ```scala
    // хорошо
    implicit val writes: Writes[Agency] = (o: Agency) => { ... }
	
    // хорошо
    implicit val writes = new Writes[Agency] {
      def writes(o: Agency): JsObject = Json.obj(
        ...
      )
    }
	```
 
* Тайпклассы для различных нужд (JSON, BSON, Redis и т. д.) нужно помещать в отдельные трейты.
  ```scala
  case class SomeModel(value: String)
  
  trait SomeModelBson { ... }
  trait SomeModelJson { ... }
  trait SomeModel extends SomeModelBson with SomeModelJson
  ```
*  **Используйте макросы** `play-json`, `Reactive Mongo` и др. везде, где это позволяет избавиться от boilerplate-кода и повысить читаемость.
* Если не все поля совпадают по имени, но совпадает большинство, то попробуйте поискать инструменты переименования полей (`JsonNaming` для `play-json`, аннотация `@Key` для `Mongo`).
* Грабли: при использовании `using` в макросах `Reactive Mongo` или `play-json` (они практически одинаковые) будет сбрасываться конфигурация:
    
    ```scala
    // из-за using snake case не применится
    implicit def config = MacroConfiguration(SnakeCase)
    implicit val handler: BSONDocumentHandler[SeanceExternal] = Macros.using[MacroOptions.ReadDefaultValues].handler
  
    // все сработает
    implicit def config: Aux[MacroOptions.ReadDefaultValues] = MacroConfiguration(SnakeCase)
    implicit val handler: BSONDocumentHandler[SeanceExternal] = Macros.handler
    ``` 
* Для работы с Mongo используйте `Reactive Mongo` (`Salat` - deprecated).

## <a name='akka'>Akka</a>

### <a name='actor_structure'>Структура актора</a>
Рассмотрим структуру работы с акторами в Play Framework на примере синхронизации оповещений:

1. В директорию `actors` добавьте файл `NotificationSyncActor.scala`:

	```scala
	package actors
	
	import akka.actor.Actor
	import com.google.inject.Inject
	import filters.Log
	import services.notification.NotificationSyncService
	
	import scala.concurrent.duration._
	import scala.concurrent.ExecutionContext
	
	object NotificationSyncActor {
	  case object PackageMissingNotificationSync
	  case object PackageDeletingNotificationSync
	  ...
	  
	  val SYNC_PERIOD = 1.hour
	}
	
	class NotificationSyncActor @Inject()(
	  notificationSyncService: NotificationSyncService
	)(implicit ex: ExecutionContext) extends Actor {
	  import NotificationSyncActor._
	
	  val logger = Log.app
	
	  logger.info("[Start notification sync service module]")
	  
	  context.system.scheduler.schedule(30.seconds, SYNC_PERIOD, self, PackageMissingNotificationSync)
	  context.system.scheduler.schedule(35.seconds, SYNC_PERIOD, self, PackageDeletingNotificationSync)
	  ...
	  
	  override def receive: Receive = {
	    case PackageMissingNotificationSync => notificationSyncService.updatePackageMissingNotifications
	    case PackageDeletingNotificationSync => notificationSyncService.updatePackageDeletedNotifications
	    ...
	  }
	}
	```

2. В директорию `services` добавьте файл `NotificationSyncService.scala` где будут располагаться методы обновления оповещений.
3. В директорию `modules` добавьте файл `NotificationModule.scala`:

	```scala
	package modules

	import actors.NotificationSyncActor
	import com.google.inject.AbstractModule
	import play.api.libs.concurrent.AkkaGuiceSupport
	
	class NotificationModule extends AbstractModule with AkkaGuiceSupport {
	  override def configure() = {
	    bindActor[NotificationSyncActor]("notification-sync-actor")
	  }
	}
	```
4. Затем, в `conf/application.conf` добавьте:
	
	```scala
	...
	
	play.modules.enabled += "modules.NotificationModule"
	
	...
	```
	
	
Cтоит сделать важное уточнение, что решение о том, распологать ли основной код в самом акторе (внутри `receive` или ниже `recieve`), либо поместить код в отдельный класс решать вам.

### <a name='ask_and_tell'>Ask и Tell</a>
* Вместо `.tell` и `.ask` используйте `!` и `?` соответсвенно.

	```scala
	// плохо
	actorFirst.tell(msg)
	actorSecond.ask(msg)
	
	// хорошо
	actorFirst ! msg
	actorSecond ? msg
	```
* Для `?` используйте неявный `timeout`.

	```scala
	// плохо
	(actorExample ? msg)(5.minutes)
	
	// хорошо
	implicit val timeout = Timeout(5.minutes)
	actorExample ? msg
	```

## <a name='performance'>Производительность</a>
Для подавляющего большинства кода, который вы пишете, производительность не должна быть проблемой. А если она возникает, то почти наверняка из-за незнания базовых структур данных и неверных представлениях о сложности используемых операций. 

Первым делом ознакомьтесь с [характеристиками производительности](https://docs.scala-lang.org/overviews/collections/performance-characteristics.html). Вероятно вы просто неправильно используете коллекции или методы.

**И если после этого вы не испытываете проблем с производительностью, то можете пропустить этот раздел.**

Однако для чувствительного к производительности кода вот несколько советов.

### <a name='microbenchmarks'>Тесты производительности</a>
Очень сложно написать хороший тест производительности, потому что компилятор Scala и JVM JIT-компилятор делают много магии для кода. Чаще всего, ваш microbenchmark не измеряет то, что вы хотите измерить.

Используйте [jmh](http://openjdk.java.net/projects/code-tools/jmh/) если вы пишете тесты производительности кода. Убедитесь, что вы прочитали [все примеры тестов производительности](http://hg.openjdk.java.net/code-tools/jmh/file/tip/jmh-samples/src/main/java/org/openjdk/jmh/samples/) так, что вы понимаете эффект устранения мертвого кода, постоянное складывание и развертывание цикла на microbenchmarks.

### <a name='while'>While</a>
Используйте `while` циклы вместо `for` или функциональных преобразований (например, `map`, `foreach`). For циклы и функциональные преобразования с точки зрения производительности очень медленные.

```scala
val points = // Array[Int]


// более низкая производительность, по сравнению с примером ниже
val newPoints = points.zipWithIndex.map { case (point, i) =>
  if (i % 2 == 0) 0 else point
}

// более высокая производительность, по сравнению с примером выше
val newPoints = new Array[Int](points.length)
var i = 0
val length = newPoints.length
while (i < length) {
  newPoints(i) = if (i % 2 == 0) 0 else points(i)
  i += 1
}
```

### <a name='option_and_null'>Option и null</a>
Для кода, чувствительного к производительности, предпочитайте `null` значение над `Option`, чтобы избежать вызовов виртуальных методов и упаковки. Четко обозначьте поля с нулевым значением с помощью `Nullable`.

```scala
@javax.annotation.Nullable
private[this] var nullableRelease: Release = _
```

### <a name='scala_collection_library'>Библиотека коллекций Scala</a>
Для кода, чувствительного к производительности, предпочитайте библиотеку коллекции Java поверх Scala, поскольку библиотека коллекции Scala часто медленнее, чем Java.

### <a name='private_this'>private[this]</a>
Для кода, чувствительного к производительности, предпочитайте `private[this]` вместо `private`, так как `private[this]` генерирует поле, а не создает метод доступа. JVM JIT-компилятор не всегда может использовать методы доступа к полю `private`, поэтому безопаснее использовать `private[this]`, что гарантирует отсутствие вызова виртуального метода для доступа к полю.


## <a name='testing'>Тестирование</a>
* Пишите тесты используя [Scalatest](https://github.com/scalatest/scalatest), [Scalatest + Play](https://github.com/playframework/scalatestplus-play) и [Mockito](https://site.mockito.org/).
* Используйте [Coverage](https://github.com/scoverage/sbt-scoverage), [Scapegoat](https://github.com/sksamuel/sbt-scapegoat), [SonarQube](https://github.com/mwz/sbt-sonar) для определения покрытия кода тестами, статического анализа кода и управлением качеством кода соответственно.
* Тесты должны находиться в директории `test` вашего проекта.
* Каждая спецификация - это один `class`, что расширяется с помощью `PlaySpec`. Также при необходимости расширяется с помощью `MockitoSugar`, `Results`, `WordSpec`, `Matchers`, `Inspectors`.
* Названия класса спецификации должны быть таким же, что и название класса, для которого пишется тест с приставкой `Spec` на конце. Например, если вы хотите протестировать `ExampleController.scala`, то ваш файл с тестами должен называться `ExampleControllerSpec.scala`.
* Поместите каждую спецификацию в свой собственный файл.

Пример теста:

```scala
package utils

import org.scalatestplus.play.PlaySpec
import utils.premiere.Helpers.StringExtended

class HelpersSpec extends PlaySpec {
  import HelpersSpec._

  texts.zipWithIndex.foreach {
    case ((encodedText, text), index) => {
      s"be valid decode text $index" in {
        encodedText.decode mustBe text.toCheckDecode
      }
    }
  }

  texts.zipWithIndex.foreach {
    case ((encodedText, text), index) => {
      s"be valid encode text $index" in {
        text.encode mustBe encodedText
      }
    }
  }
}

object HelpersSpec {
  val texts = ...
}
```


## <a name='documentation'>Документирование</a>

### <a name='commentary'>Комментарии</a>
* Используйте комментарии в особенных случаях (например, для обозначения временного куска кода, который вы в дальнейшем перепишете, или пояснение к куску кода, который реализован не очевидным образом). 
* Для остальных случаев старайтесь не использовать комментарии.

### <a name='apidoc'>Apidoc</a>
Для наших методов API в контроллерах мы используем синтаксис документирования [Apidoc](#http://apidocjs.com/). 

Придерживайтесь следующих рекомендаций:

* Пишите документацию к API таким образом, чтобы у того, кто будет ее читать не возникало вопросов.
*  Используйте все необходимые теги в зависимости от метода API и принимаемых/отдаваемых параметров: 
	* Если используются `headers`, то укажите `@apiHeader` и `@apiHeaderExample`.
	* Если на вход поступает `json`, то укажите все описания полей через `@apiParam (body)` и пример входящего `json` через `@apiParamExample {json}`.
	* Если ваше апи может вернуть 4xx/5xx ошибку, то укажите `@apiErrorExample` со всеми возмоными ошибками.
	* и т.д., более подробно про теги можете почитать на [Apidoc](#http://apidocjs.com/).
* Не забывайте опциональные поля обрамлять `[]`.
* Если есть поле-перечесление, то описывайте все возможные значения этого поля.

### <a name='scaladoc'>Scaladoc</a>
* В качестве документирования кода используйте встроенный scaladoc и соответствующий [стиль написания](https://docs.scala-lang.org/overviews/scaladoc/for-library-authors.html).
* Пишите документацию только на сложную бизнес логику, алгоритмы, неординарные вещи.

## <a name='git'>Git</a>
* Склеивайте несколько коммитов с одинаковым назначением в один. Удобнее всего это делать через `--fixup` и `--autosquash`, но можно и просто через `rebase`.

## <a name='miscellaneous'>Разное</a>

### <a name='architecture'>Архитектура</a>

* Старайтесь делать контроллеры тонкими.

* Старайтесь делать акторы также тонкими, то есть отвечающими за отправку сообщений и делегирующими остальную работу сервисам.
Возможно нарушение этого принципа, если сам актор рассматривается как сервис (в нем нет особой логики обмена сообщениями, например простой алгоритм, выполняющийся по расписанию).

* Место использования DAO - всегда сервис (или актор, если мы рассматриваем его как сервис).

### <a name='tech_debt'>Технический долг</a>

* **Копипаст в рамках одного репозитория запрещен**. В особых случаях это допустимо, но должно обсуждаться.
    
    Если код переиспользуется несколькими подпроектами, то он должен выноситься в еще один подпроект, примерно где-то в папке `base` или `common`. 
    Подпроекты должны быть адекватного размера, не слишком большими, но и достаточно маленькими, разбитыми по зонам ответственности.

* Документирование архитектуры: описывать все ключевые моменты принятых архитектурных решений (лучше всего это писать в `README.md`). Например:
    * Что сохраняется во фронтенде, чтобы избежать лишних обращений к БД.
    * Можно или нет запускать проект в нескольких экземплярах.
    * Почему выбран такой период синхронизации.
    * Какие части проекта совместно используют какие данные, API и др.
    * Связь проекта с другими проектами.
    * Dataflow diagrams.
    * Почему была выбрана та или иная технология (если это неочевидно).
    
    Другими словами все, что поможет принять оптимальные решения при последующих доработках проектов.

### <a name='not_reinventing_the_wheel'>Не изобретай колесо</a>
Когда есть существующий хорошо проверенный метод или библиотека или фреймворк, которые способны решить вашу задачу и не имеют проблем с производительностью, то используйте их. Переосмысление такого метода может привести к ошибкам и потребовать время на написание тестов (если вы не забудете их написать!). Если вы не знаете о существующих технологиях, то потратьте время на исследование. Как правило, нескольких часов поиска в интернете хватает, для того, чтобы определить варианты решения вашей задачи.

```scala
// плохо, зачем плодить новый класс если есть готовая реализация ниже
class Sha256 {
  ...
}

// хорошо
deg toSha256Hex(value: String) = MessageDigest.getInstance(SHA256).digest(value).toHex
```

Исключения

* Использование существующего метода требует добавления новой зависимости. Если такой метод довольно прост, то переосмысление его лучше, чем добавление зависимости. Но не забудьте проверить это.
* Существующий метод не оптимизирован для использования и слишком медленный. Но сначала проверьте его, избегайте преждевременной оптимизации.
* Существующие реализации не решают поставленную задачу.

### <a name='boy_scout_rule'>Правило бойскаута</a>
Правило бойскаута: 
> Оставь место стоянки чище, чем оно было до твоего прихода.

Из книги Роберта Мартина **Чистый код**:
> Если мы все будем оставлять свой код чище, чем он был до нашего прихода, то код попросту не будет загнивать. Чистка не обязана быть глобальной. Присвойте более понятное имя переменной, разбейте слишком большую функцию, устраните одно незначительное повторение, упростите сложную цепочку условий.
Представляете себе работу над проектом, код которого улучшается с течением времени? Но может ли профессионал позволить себе нечто иное? Разве постоянное совершенствование не является неотъемлемой частью профессионализма?

В соответствии с этими правилами, старайся следовать следущим рекомендациям:

* Если ты добавляешь новые методы в класс, то поправь код-стайл во всем файле в соответствии с правилами, описанными в этом гайде.
* Старайся делать такой рефакторинг отдельным коммитом, а не в том же коммите, в котором ты сделал таску. Так, разработчику будет легче ревьювить твой код.
* Если рефакторинг очень большой, или есть потребность переписать логику всего кода, то старайся делать это в отдельной ветке.
