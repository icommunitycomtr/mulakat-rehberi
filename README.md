# Mülakat Rehberi

## Genel
Mülakatta sorulan teknik sorular dışındaki tüm sorular.

* Maaş beklentin nedir

## Teknik
Mülakatta sorulan sadece teknik sorular.

### Sorular
- [1 - Class ve Struct’ın Farkları](#1---class-ve-structın-farkları)
- [2 - Protocol-Oriented Programming (POP) vs. Object-Oriented Programming (OOP)](#2---protocol-oriented-programming-pop-vs-object-oriented-programming-oop)
- [3 - Uygulamanın Yaşam Döngüsü (App Lifecycle)](#3---uygulamanın-yaşam-döngüsü-app-lifecycle)
- [4 - ViewController’ın Yaşam Döngüsü (View Lifecycle)](#4---viewcontrollerın-yaşam-döngüsü-view-lifecycle)
- [5 - Extension Nedir? Neden, Nasıl ve Ne Zaman Kullanılır?](#5---extension-nedir-neden-nasıl-ve-ne-zaman-kullanılır)
- [6 - Escaping ve Non-Escaping Closures Nedir?](#6---escaping-ve-non-escaping-closures-nedir)
- [7 - Array, Set, Dictionary Arasındaki Fark Nedir?](#7---array-set-dictionary-arasındaki-fark-nedir)
- [8 - High Order Functions Nelerdir?](#8---high-order-functions-nelerdir)
- [9 - ARC (Automatic Reference Counting) Nedir? Ne İşe Yarar?](#9---arc-automatic-reference-counting-nedir-ne-i̇şe-yarar)
- [10 - Optional ve Unwrap Nedir? Ne İşe Yarar?](#10---optional-ve-unwrap-nedir-ne-i̇şe-yarar)
- [11 - Access Level (Erişim Seviyeleri) Nedir?](#11---access-level-erişim-seviyeleri-nedir)
- [12 - SOLID Prensipleri](#12---solid-prensipleri)


# 1 - Class ve Struct’ın Farkları

### Genel Tanım
Swift programlama dilinde **Class (Sınıf)** ve **Struct (Yapı)**, veri modelleri oluşturmak için kullanılan iki temel yapıdır. Her ikisi de **özellik (property)** ve **metot (method)** tanımlayabilir, ancak çalışma şekilleri açısından önemli farklılıklar içerir.

- **Class (Sınıf)**, referans tipidir (**Reference Type**). Instance'ları genellikle **heap** üzerinde tutulur ve değişkenler arasında referans olarak paylaşılır. Aynı nesne farklı değişkenler tarafından paylaşılabilir, bu da **paylaşılan değişikliklere (shared mutation)** olanak tanır.
- **Struct (Yapı)**, değer tipidir (**Value Type**). Genellikle **stack** üzerinde saklanır ve her atamada kopyalanarak yeni bir bağımsız örnek oluşturulur. Ancak bu kesin bir kural değildir: bir class'ın property'si olan, bir closure tarafından yakalanan veya `any Protocol` içinde tutulan büyük bir struct heap'te saklanabilir.

Value semantics, **paylaşılan değiştirilebilir durumdan (shared mutable state)** kaçınmayı kolaylaştırır. Ancak struct **kendiliğinden thread-safe değildir**: birden fazla thread'in aynı `var` değişkene eriştiği durumda data race oluşabilir.

Swift, özellikle **Struct** kullanımını teşvik eder, çünkü Struct'lar öngörülebilir (kopyalanan veri başka bir yerden değiştirilemez) ve çoğu durumda **daha performanslıdır**. Ancak, **Class** kullanımının da gerekli olduğu bazı durumlar vardır.

### Temel Farklar

| Özellik                | Class                     | Struct                     |
|------------------------|--------------------------|----------------------------|
| **Referans Tipi**      | Evet (Reference Type)  | Hayır (Value Type)      |
| **Bellek Yönetimi**    | Genellikle heap üzerinde | Genellikle stack üzerinde (class içinde, closure'da yakalanınca veya `any` içinde heap'te olabilir) |
| **Kopyalama Davranışı**| Referans üzerinden paylaşılır (Shared Reference) | Değer olarak kopyalanır. Copy-on-Write otomatik değildir; yalnızca Array, Dictionary, Set ve String gibi standart tiplerde vardır, kendi struct'ınızda elle yazılır |
| **Inheritance (Kalıtım)** | Destekler       | Desteklemez            |
| **Mutability (Değiştirilebilirlik)** | `let` ile tutulan instance'ın `var` property'leri değiştirilebilir | `let` ile tutulan instance hiç değiştirilemez; `self`'i değiştiren metotlar `mutating` olmalıdır |
| **ARC (Automatic Reference Counting)** | Evet | Hayır |
| **Deinitializers** | `deinit` metodu vardır | Genellikle yoktur; yalnızca noncopyable (`~Copyable`) struct'larda `deinit` yazılabilir (Swift 5.9+) |

### Class Kullanımı

```swift
class Person {
    var name: String

    init(name: String) {
        self.name = name
    }
}

let person1 = Person(name: "Ali")
let person2 = person1
person2.name = "Veli" // person2 `let` olmasına rağmen property değişebilir

print(person1.name) // "Veli" (Referans tip olduğu için aynı nesne değiştirildi.)
```

### Struct Kullanımı

```swift
struct Person {
    var name: String

    mutating func rename(to newName: String) { // self'i değiştirdiği için mutating
        name = newName
    }
}

var person1 = Person(name: "Ali")
var person2 = person1
person2.name = "Veli"

print(person1.name) // "Ali" (Kopya oluşturuldu, orijinal veri değişmedi.)

let person3 = Person(name: "Ayşe")
// person3.name = "Fatma" // Derleme hatası: `let` struct değiştirilemez
```

## Hangi Durumda Hangisi Kullanılmalı?
### Struct Kullanımı Tercih Edilmeli
- Küçük ve hafif veri tipleri için (örneğin `CGPoint`, `CGSize`, `CGRect`).
- Immutable (değişmez) veriler için.
- Kopyalama işlemlerinin maliyetinin düşük olduğu durumlarda.
- Thread'ler arasında paylaşılan değiştirilebilir durumdan kaçınmak istendiğinde (value semantics yardımcı olur, ancak tek başına thread-safety garantisi değildir).

### Class Kullanımı Tercih Edilmeli
- Nesneler arasında referans paylaşımı gerektiğinde.
- Kalıtım (Inheritance) kullanılacaksa.
- Büyük ve kompleks veri modellerinde, özellikle UIKit gibi referans bazlı framework’lerde.
- Nesne yaşam döngüsünü yönetmek için `deinit` fonksiyonuna ihtiyaç duyulduğunda.

---

# 2 - Protocol-Oriented Programming (POP) vs. Object-Oriented Programming (OOP)

### Genel Tanım
Swift dili, geleneksel **Nesne Yönelimli Programlama (OOP)** kısıtlamalarından kaçınmak için **Protokol Tabanlı Programlama (POP)** anlayışını teşvik eder. Ancak iOS geliştirme süreçlerinde hem OOP hem de POP bir arada kullanılabilir. Kullanım amacına göre en uygun yaklaşım tercih edilmelidir.

- **OOP (Object-Oriented Programming)**, **class** kullanarak veri ve davranışları bir araya getiren bir paradigmadır. Kalıtım (**inheritance**) yoluyla nesneler arası ilişkiler kurulur.
- **POP (Protocol-Oriented Programming)**, kalıtım yerine **protokolleri** temel alarak kodun daha esnek, test edilebilir ve genişletilebilir olmasını sağlar.

Swift, **struct ve enum gibi değer türleriyle (value types) çalışmayı ve çoklu kalıtım yerine protokolleri kullanmayı teşvik ettiği için,** POP yaklaşımı dilin doğal yapısına daha uygundur.

### Temel Farklar: POP vs. OOP

| Özellik                      | OOP (Object-Oriented Programming) | POP (Protocol-Oriented Programming) |
|------------------------------|----------------------------------|----------------------------------|
| **Temel Yapı**               | Class (Sınıf) merkezlidir        | Protocol (Protokol) merkezlidir |
| **Miras (Inheritance)**      | Swift'te tekli kalıtım (single inheritance) destekler | Çoklu protokol uyumu mümkündür |
| **Çoklu Kalıtım**            | Swift'te class'lar desteklemez (tek bir üst sınıftan türetilir); C++ gibi bazı OOP dillerinde vardır | Bir tür birden fazla protokole uyabilir |
| **Bellek Yönetimi**          | Class’lar heap üzerinde tutulur (ARC ile yönetilir) | Struct ve enum kullanımı teşvik edilir; ancak `any Protocol` (existential) kullanımı heap allocation ve dynamic dispatch getirebilir |
| **Kod Yeniden Kullanımı**    | Alt sınıflar (subclass) ile sağlanır | Protokol genişletmeleri (protocol extensions) ile sağlanır |
| **Esneklik**                 | Katı ve hiyerarşik bir yapı vardır | Daha modüler ve genişletilebilir yapı sunar |
| **Test Edilebilirlik**       | Bağımlılıklar fazla olabilir, test edilmesi zorlaşır | Test etmek ve mock'lamak daha kolaydır |

### OOP Kullanımı (Class Tabanlı Miras)

```swift
class Animal {
    var name: String

    init(name: String) {
        self.name = name
    }

    func makeSound() {
        print("Some sound")
    }
}

class Dog: Animal {
    override func makeSound() {
        print("Bark!")
    }
}

let myDog = Dog(name: "Rex")
myDog.makeSound() // "Bark!"
```

### POP Kullanımı (Protocol Tabanlı Tasarım)

```swift
protocol SoundMaker {
    func makeSound()
}

protocol Named {
    var name: String { get }
}

struct Dog: SoundMaker, Named {
    var name: String

    func makeSound() {
        print("Bark!")
    }
}

let myDog = Dog(name: "Rex")
myDog.makeSound() // "Bark!"
```

### Protocol Extension'da Dispatch Tuzağı
Protokol gereksinimi olan metotlar **dynamic dispatch** ile çağrılır (türün kendi uygulaması çalışır). Yalnızca protocol extension'da tanımlanıp protokol gereksinimi **olmayan** metotlar ise **static dispatch** ile çağrılır: değişkenin tipi protokol ise türün kendi uygulaması değil, extension'daki uygulama çalışır.

```swift
protocol Greeter {
    func greet() // protokol gereksinimi
}

extension Greeter {
    func greet() { print("Merhaba (default)") }
    func farewell() { print("Hoşça kal (default)") } // gereksinim DEĞİL
}

struct TurkishGreeter: Greeter {
    func greet() { print("Selam") }
    func farewell() { print("Görüşürüz") }
}

let greeter: any Greeter = TurkishGreeter()
greeter.greet()    // "Selam"               → dynamic dispatch (gereksinim)
greeter.farewell() // "Hoşça kal (default)" → static dispatch (yalnızca extension'da)

let turkish = TurkishGreeter()
turkish.farewell() // "Görüşürüz" (tip somut olduğu için türün metodu çalışır)
```

## Hangi Durumda Hangisi Kullanılmalı?
### OOP Kullanımı Tercih Edilmeli:
- Büyük ve karmaşık nesne yapılarında.
- UIKit gibi class tabanlı framework’lerle çalışırken.
- Referans tipli nesnelerin paylaşılması gerekiyorsa.
- Nesneler arasında miras ve davranış paylaşımı önemliyse.

### POP Kullanımı Tercih Edilmeli:
- Değer tipi (struct, enum) kullanımı gerekiyorsa.
- Kodun daha modüler ve test edilebilir olması isteniyorsa.
- Bir türün birden fazla davranış setini (protokolü) bir arada taşıması gerekiyorsa.
- Esnek ve genişletilebilir bir yapı oluşturulması isteniyorsa.
---

# 3 - Uygulamanın Yaşam Döngüsü (App Lifecycle)

### Genel Tanım
iOS uygulamalarında, sistem tarafından yönetilen ve uygulamanın **arka plana alınması, kapatılması veya yeniden başlatılması** gibi durumları yöneten bir yaşam döngüsü mekanizması bulunur. 

**UIApplicationDelegate ve UIWindowSceneDelegate** protokolleri (SwiftUI'da `App` ve `scenePhase`) sayesinde, uygulamanın bu yaşam döngüsündeki önemli olayları yakalayarak uygun işlemleri gerçekleştirebiliriz.

### Uygulama Yaşam Döngüsü Aşamaları

| Aşama               | Açıklama |
|---------------------|------------------------------------------------------------|
| **Not Running**     | Uygulama başlatılmamış veya kapatılmış durumda.           |
| **Inactive**        | Uygulama ön planda ancak kullanıcı etkileşimde değil.     |
| **Active**          | Uygulama ön planda ve kullanıcı ile etkileşimde.          |
| **Background**      | Uygulama arka planda çalışıyor, ancak aktif olarak görünmüyor. |
| **Suspended**       | Uygulama bellekte tutuluyor ama işlem yapmıyor. Sistem bu durumdaki uygulamayı haber vermeden sonlandırabilir. |

## Uygulama Yaşam Döngüsü Metotları
### UIApplicationDelegate ile Yönetim
iOS 13 öncesinde tüm yaşam döngüsü olayları AppDelegate'te yönetilirdi. iOS 13 ve sonrasında AppDelegate **hâlâ vardır** (uygulama başlatma, push bildirim kaydı, scene konfigürasyonu vb.), ancak scene kullanan uygulamalarda foreground/background gibi UI yaşam döngüsü olayları SceneDelegate'e taşınmıştır. Bu durumda aşağıdaki `applicationWillResignActive`, `applicationDidEnterBackground` vb. metotlar çağrılmaz.

```swift
@main // Eski projelerdeki @UIApplicationMain deprecated oldu
class AppDelegate: UIResponder, UIApplicationDelegate {
    
    func application(_ application: UIApplication,
                     didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        print("Uygulama başlatıldı.")
        return true
    }
    
    func applicationWillResignActive(_ application: UIApplication) {
        print("Uygulama geçici olarak aktif değil.")
    }

    func applicationDidEnterBackground(_ application: UIApplication) {
        print("Uygulama arka plana alındı.")
    }

    func applicationWillEnterForeground(_ application: UIApplication) {
        print("Uygulama arka plandan döndü.")
    }

    func applicationDidBecomeActive(_ application: UIApplication) {
        print("Uygulama tekrar aktif.")
    }

    func applicationWillTerminate(_ application: UIApplication) {
        print("Uygulama kapatılıyor (çağrılması garanti değildir).")
    }
}
```

### UIWindowSceneDelegate ile Yönetim (iOS 13 ve Sonrası)
```swift
class SceneDelegate: UIResponder, UIWindowSceneDelegate {

    var window: UIWindow?

    func scene(_ scene: UIScene,
               willConnectTo session: UISceneSession,
               options connectionOptions: UIScene.ConnectionOptions) {
        guard let windowScene = scene as? UIWindowScene else { return }
        window = UIWindow(windowScene: windowScene)
        window?.rootViewController = MyViewController()
        window?.makeKeyAndVisible()
        print("Sahne bağlandı.")
    }

    func sceneDidDisconnect(_ scene: UIScene) {
        print("Sahne bağlantısı kesildi (sistem kaynak açmak için sahneyi bırakabilir).")
    }

    func sceneDidBecomeActive(_ scene: UIScene) {
        print("Sahne aktif oldu.")
    }

    func sceneWillResignActive(_ scene: UIScene) {
        print("Sahne geçici olarak aktif değil.")
    }

    func sceneDidEnterBackground(_ scene: UIScene) {
        print("Sahne arka plana geçti.")
    }

    func sceneWillEnterForeground(_ scene: UIScene) {
        print("Sahne arka plandan döndü.")
    }
}
```

### SwiftUI ile Yönetim (`App` ve `scenePhase`)
```swift
@main
struct MyApp: App {
    @Environment(\.scenePhase) private var scenePhase

    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        .onChange(of: scenePhase) { _, newPhase in
            switch newPhase {
            case .active: print("Sahne aktif.")
            case .inactive: print("Sahne geçici olarak aktif değil.")
            case .background: print("Sahne arka planda.")
            @unknown default: break
            }
        }
    }
}
```

**Not:** Apple, iOS 26'dan sonraki büyük sürümde en güncel SDK ile derlenen uygulamalar için UIScene yaşam döngüsünün zorunlu olacağını duyurdu. Yeni projelerde SceneDelegate (veya SwiftUI `App`) kullanılmalıdır.

## Uygulama Yaşam Döngüsünde Yapılabilecek İşlemler
### `didFinishLaunchingWithOptions`
- Uygulama başlatıldığında gerekli başlangıç ayarlarını yap.
- Push bildirimlerini kontrol et.
- Veri tabanı bağlantısını başlat.

### `applicationWillResignActive`
- Kullanıcı çağrı aldıysa veya ekran kilitlendiyse çalışır.
- Aktif olmayan durumlarda, oyunları duraklat veya medya oynatmayı durdur.

### `applicationDidEnterBackground`
- Kullanıcının son verilerini kaydet. Kalıcı kayıt için en güvenli yer burasıdır (scene kullanan uygulamalarda `sceneDidEnterBackground`).
- Açık ağ bağlantılarını kapat ve CPU tüketimini azalt.
- Arka planda çalışacak işlemleri (örn. müzik çalma) yönet.
- Bu metot için yaklaşık 5 saniye süre vardır; daha uzun işler için `beginBackgroundTask(expirationHandler:)` kullanılır.

### `applicationWillEnterForeground`
- Uygulama yeniden ön plana dönerken animasyonlar veya UI güncellemeleri yap.
- Kullanıcının bıraktığı yerden devam etmesini sağla.

### `applicationDidBecomeActive`
- Kullanıcı uygulamaya geri döndüğünde medya oynatmayı başlat veya güncellemeleri kontrol et.

### `applicationWillTerminate`
- **Çağrılması garanti değildir:** suspended durumdaki uygulama sistem tarafından bu metot çağrılmadan sonlandırılabilir.
- Bu yüzden kritik veri kaydı burada değil, `applicationDidEnterBackground` / `sceneDidEnterBackground` içinde yapılmalıdır.
- Burada yalnızca son temizlik işlemleri yapılabilir.
---

# 4 - ViewController’ın Yaşam Döngüsü (View Lifecycle)

### Genel Tanım
iOS'ta **UIViewController**, bir ekranın veya bileşenin yaşam döngüsünü yöneten temel sınıftır. Her **ViewController**, **UIKit** tarafından belirli bir sırayla çağrılan metodlara sahiptir ve bu metotlar, ekranın yüklenmesi, görünmesi, güncellenmesi ve yok edilmesi gibi süreçleri kontrol etmemizi sağlar.

ViewController yaşam döngüsünü anlamak, **UI güncellemelerini optimize etmek, bellek yönetimini iyileştirmek ve performansı artırmak** için önemlidir.

### ViewController Yaşam Döngüsü Aşamaları

| Metot                  | Açıklama |
|------------------------|------------------------------------------------------------|
| `loadView()`           | View oluşturulurken çağrılır. Kendi view'ını koddan oluşturmak için override edilir; bu durumda `super.loadView()` çağrılmaz. Aksi halde dokunulmaz. |
| `viewDidLoad()`        | View'a ilk kez erişildiğinde (lazy) yüklenip hafızaya alındıktan sonra **bir kez** çağrılır. UI kurulumu ve ilk veri yükleme burada yapılır. |
| `viewWillAppear()`     | View ekrana gelmeden hemen önce çağrılır. UI güncellemeleri ve animasyon hazırlıkları burada yapılır. |
| `viewIsAppearing()`    | View hiyerarşiye eklendikten ve trait/boyut bilgileri hazır olduktan sonra, `viewDidAppear`'dan önce çağrılır (iOS 17 SDK, iOS 13'e kadar geriye uyumlu). Boyuta/trait'e bağlı UI güncellemeleri için en doğru yerdir. |
| `viewWillLayoutSubviews()` | Alt view'lar yerleştirilmeden önce çağrılır. Layout değiştikçe (döndürme vb.) tekrar çağrılabilir. |
| `viewDidLayoutSubviews()`  | Alt view'lar yerleştirildikten sonra çağrılır. Frame'e bağlı ayarlamalar burada yapılır; birden çok kez çağrılabilir. |
| `viewDidAppear()`      | View ekrana geldikten sonra çağrılır. Animasyonlar ve ekran görüntüleme olayları burada başlatılır. |
| `viewWillDisappear()`  | View ekrandan kaybolmadan hemen önce çağrılır. Kullanıcının yaptığı değişiklikleri kaydetmek için uygundur. |
| `viewDidDisappear()`   | View tamamen ekrandan kaybolduğunda çağrılır. Ağ isteklerini iptal etmek veya temizlik işlemleri için uygundur. |
| `deinit`               | UIViewController'a özgü bir lifecycle metodu değil, Swift'in deinitializer'ıdır. ViewController bellekten tamamen kaldırıldığında çağrılır. **Retain cycle varsa hiç çağrılmaz**; bu yüzden memory leak tespiti için kullanılır. |

## ViewController Yaşam Döngüsü Metotları
### Temel Yaşam Döngüsü Metotları
```swift
class MyViewController: UIViewController {
    
    override func loadView() {
        // Kendi view'ımızı oluşturuyorsak super.loadView() ÇAĞRILMAZ.
        view = UIView()
        print("loadView çağrıldı.")
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        print("viewDidLoad çağrıldı.")
        setupUI()
    }

    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        print("viewWillAppear çağrıldı.")
    }

    override func viewIsAppearing(_ animated: Bool) {
        super.viewIsAppearing(animated)
        print("viewIsAppearing çağrıldı.")
    }

    override func viewWillLayoutSubviews() {
        super.viewWillLayoutSubviews()
        print("viewWillLayoutSubviews çağrıldı.")
    }

    override func viewDidLayoutSubviews() {
        super.viewDidLayoutSubviews()
        print("viewDidLayoutSubviews çağrıldı.")
    }

    override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)
        print("viewDidAppear çağrıldı.")
    }

    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        print("viewWillDisappear çağrıldı.")
    }

    override func viewDidDisappear(_ animated: Bool) {
        super.viewDidDisappear(animated)
        print("viewDidDisappear çağrıldı.")
    }

    deinit {
        print("ViewController bellekten silindi (deinit çağrıldı).")
    }
}
```

## Hangi Aşamada Ne Yapılmalı?

### `loadView()`
- `UIViewController`'ın view'ını tamamen koddan, özel bir `UIView` olarak oluşturmak istiyorsan override et ve `view` property'sine ata.
- Bu durumda `super.loadView()` **çağırma** (Apple dokümanına göre özel uygulamalar super'i çağırmamalıdır).
- Storyboard/XIB kullanıyorsan veya özel bir view gerekmiyorsa varsayılan haliyle bırak.

### `viewDidLoad()`
- UI bileşenlerini oluştur ve konumlandır.
- Ekran için gereken ilk veriyi API veya veritabanından çek (bir kez çalıştığı için ilk yükleme burada yapılır).
- İlk yükleme sırasında tek seferlik ayarlamaları yap.

### `viewWillAppear()`
- Kullanıcı her ekrana geldiğinde güncellenmesi gereken verileri burada yenile.
- Navigation Bar veya UI değişikliklerini yap.

### `viewIsAppearing()`
- View'ın boyutuna veya trait'lerine (ör. size class) bağlı UI güncellemelerini yap.
- Bu noktada view hiyerarşidedir ve geometri bilgisi doğrudur.

### `viewWillLayoutSubviews()` / `viewDidLayoutSubviews()`
- Frame'e bağlı ayarlamaları (ör. köşe yuvarlama, gölge path'i) `viewDidLayoutSubviews` içinde yap.
- Birden çok kez çağrılabildikleri için ağır işler ve tek seferlik kurulumlar burada yapılmamalı.

### `viewDidAppear()`
- Animasyonları başlat.
- Timer'ları ve analytics ekran görüntüleme olaylarını başlat.

### `viewWillDisappear()`
- Kullanıcının yaptığı değişiklikleri kaydet.
- Açık kalmış animasyonları veya geçici UI öğelerini durdur.

### `viewDidDisappear()`
- Hafızada gereksiz nesneleri temizle.
- Arka planda çalışan görevleri iptal et.
- UI için fazla bellek kullanan nesneleri serbest bırak.

### `deinit`
- ViewController bellekten tamamen kaldırılırken çalışır (Swift deinitializer'ı).
- Block tabanlı NotificationCenter observer'larını (`addObserver(forName:object:queue:using:)`) kaldır. Selector tabanlı observer'lar iOS 9'dan beri otomatik kaldırılır.
- Açık ağ bağlantılarını serbest bırak.
- `deinit` hiç çağrılmıyorsa büyük olasılıkla bir retain cycle (memory leak) vardır.

## Yaşam Döngüsünün Çalışma Sırası
```text
Bir ViewController ekrana yüklendiğinde şu sıra izlenir:
1️⃣ loadView()
2️⃣ viewDidLoad()
3️⃣ viewWillAppear()
4️⃣ viewIsAppearing()
5️⃣ viewWillLayoutSubviews() / viewDidLayoutSubviews() (layout değiştikçe tekrar çağrılabilir)
6️⃣ viewDidAppear()
ViewController kapatıldığında veya başka bir sayfaya geçildiğinde ise:
7️⃣ viewWillDisappear()
8️⃣ viewDidDisappear()
9️⃣ Eğer ViewController tamamen yok oluyorsa → deinit (retain cycle yoksa)
```
---

# 5 - Extension Nedir? Neden, Nasıl ve Ne Zaman Kullanılır?

### Genel Tanım
**Extension**, Swift’te mevcut bir sınıf (class), yapı (struct), numaralandırma (enum) veya protokole (protocol) **yeni özellikler ve işlevler eklemeye** olanak tanır.  
Ancak, bir extension var olan türün **orijinal kaynak kodunu değiştirmeden** yeni yetenekler ekler, yani **override işlemi yapılamaz**.

### Extension Kullanım Amaçları

```text
✅ Mevcut bir sınıfa veya yapıya yeni metotlar ve computed property'ler eklemek.
✅ UIKit gibi Apple’ın framework’lerindeki sınıfları genişletmek.
✅ Kod tekrarını azaltarak daha temiz ve modüler bir yapı oluşturmak.
✅ Protokollere varsayılan implementasyon eklemek.
```

### Extension Neler Ekleyebilir, Neler Ekleyemez?

| Ekleyebilir | Ekleyemez |
|-------------|-----------|
| Computed property (instance ve static) | **Stored property** |
| Instance ve type (static) metotları | Class'lara designated initializer |
| Initializer (class'larda yalnızca `convenience init`) | Mevcut metot/property'leri override etmek |
| Subscript | Property observer (`didSet`/`willSet`) eklemek |
| Nested type (iç içe tip) | |
| Protocol conformance (`where` ile koşullu uyum dahil) | |

```swift
extension String {
    var isBlank: Bool { trimmingCharacters(in: .whitespaces).isEmpty } // ✅ computed property
    // var cache: String = ""  // ❌ Derleme hatası: Extensions must not contain stored properties
}
```

## Extension Nasıl Kullanılır?
### Temel Extension Kullanımı
```swift
extension String {
    func reversedString() -> String {
        return String(self.reversed())
    }
}

let text = "Swift"
print(text.reversedString()) // "tfiwS"
```
### Hangi Durumda Kullanılmalı?

```text
✅ Standart Swift kütüphanesindeki türlere yeni özellikler eklemek için.
✅ UIKit ve diğer framework’lerdeki sınıflara yeni işlevler kazandırmak için.
✅ Protokollere varsayılan uygulamalar eklemek için.
✅ Daha temiz ve modüler kod yazmak için.
```

### Extension Kullanımının Avantajları
```text
✅ Mevcut kodu değiştirmeden, modüler genişletmeler sağlar.
✅ Override etme gerekliliği olmadan yeni işlevler eklenebilir.
✅ Framework’ler ve üçüncü taraf kütüphaneler ile uyumlu hale getirmek için kullanılabilir.
✅ Generic yapılarla birlikte kullanılabilir.
```
## Protokollere Extension Yazılabilir Mi?
Evet! Swift’te protokollere extension ekleyerek varsayılan uygulamalar tanımlayabiliriz.
Bu, özellikle Protocol-Oriented Programming (POP) paradigmasının önemli bir parçasıdır.

## Protokol Extension Örneği
```swift
protocol Greetable {
    func greet()
}

extension Greetable {
    func greet() {
        print("Merhaba!")
    }
}

struct Person: Greetable {}
let user = Person()
user.greet() // "Merhaba!" (Varsayılan uygulama çalışıyor)
```
Bu örnekte Person yapısı Greetable protokolünü uyguladı, ancak greet() metodunu tanımlamadı.
Protokol extension sayesinde, varsayılan bir implementasyon sunmuş olduk.

**Önemli Not**: Bir protokole uyan tür, protokolün tüm gereksinimlerini karşılamak zorundadır. Bir gereksinime protokol extension'ı ile varsayılan uygulama verilirse, uyan türün o metodu yazma zorunluluğu kalkar.

Bu, Objective-C'deki **optional** gereksinimlerden farklı bir kavramdır. Örneğin bir **CollectionView** tanımlayıp **UICollectionViewDataSource** protokolünü implement ettiğinizde **numberOfItemsInSection** ve **cellForItemAt** fonksiyonlarını eklemek zorundasınız. **numberOfSections** ise bir protokol extension'ından gelmez: protokol `@objc` olduğu ve bu metot `optional` olarak tanımlandığı için zorunlu değildir (uygulanmazsa sistem 1 section varsayar).

```swift
@objc protocol MyDataSource {
    func numberOfItems() -> Int        // zorunlu
    @objc optional func numberOfSections() -> Int // optional gereksinim (yalnızca @objc protokollerde)
}
```

### Protokol Extension Kullanımının Avantajları
```text
✅ Protokolü implemente eden tüm türlere varsayılan bir davranış ekler.
✅ Kod tekrarını azaltır ve temiz bir yapı sağlar.
✅ Mevcut türlere yeni işlevler kazandırırken, esnekliği korur.
✅ Özellikle generic yapılar ve Swift standard library’de yaygın olarak kullanılır.
```
---

# 6 - Escaping ve Non-Escaping Closures Nedir?

### Genel Tanım  
Swift’te **closure’lar**, fonksiyonların parametre olarak aldığı kod bloklarıdır. Ancak closure’ların fonksiyon içindeki davranışına göre iki farklı türü vardır:  

- **Non-Escaping Closure:** Closure, çağrıldığı fonksiyon içinde çalıştırılır ve fonksiyon tamamlandığında bellekten temizlenir. Varsayılan olarak tüm closure parametreleri **non-escaping** olarak kabul edilir.  
- **Escaping Closure:** Closure, fonksiyon tamamlandıktan sonra da çalışabilir. Bir değişkene atanarak saklanabilir veya asenkron bir işlemde kullanılabilir. **@escaping** ile belirtilmesi gerekir.  

Swift’in varsayılan yaklaşımı **non-escaping** olduğu için, sadece gerektiğinde **@escaping** kullanılır.  

**Not:** Optional closure parametreleri (`(() -> Void)?`) zaten escaping kabul edilir; `@escaping` yazılmaz (yazılamaz).

### Escaping ve Non-Escaping Farkları  

| Özellik                 | Non-Escaping Closure | Escaping Closure |
|-------------------------|---------------------|-----------------|
| **Fonksiyon bitince çalışır mı?** | Hayır | Evet |
| **Bellekte ne kadar tutulur?** | Geçici (derleyici genellikle stack'te tutar) | Daha uzun süre tutulabilir (heap bellekte saklanır) |
| **Performans etkisi** | Daha hızlı ve güvenli | Dikkatli yönetilmezse memory leak olabilir |
| **`self` kullanımı** | `self.` yazmak gerekmez | Class içinde `self` açıkça yazılmalı/yakalanmalı (retain cycle riski) |
| **Kullanım alanları** | Hemen çalıştırılacak işlemler | Asenkron işlemler, tamamlanması geciken görevler |


### Non-Escaping Closure  

Non-escaping closure, fonksiyon içinde çağrılır ve fonksiyon tamamlandığında bellekten temizlenir. Daha hızlı ve güvenlidir.  

```swift
func performOperation(_ closure: () -> Void) {
    print("Fonksiyon başladı")
    closure()
    print("Fonksiyon bitti")
}

performOperation {
    print("Closure çalıştı")
}
```

```text
Çıktı:
Fonksiyon başladı
Closure çalıştı
Fonksiyon bitti
```

Closure, fonksiyon içinde çağrıldığı için **non-escaping** olarak değerlendirilir.

### Escaping Closure  

Escaping closure, fonksiyon tamamlandıktan sonra da çalıştırılabilir. Genellikle asenkron işlemlerde kullanılır veya bir değişkene atanır.  

```swift
var completionHandlers: [() -> Void] = []

func addCompletionHandler(_ handler: @escaping () -> Void) {
    completionHandlers.append(handler)
}

addCompletionHandler {
    print("Closure daha sonra çağrılacak")
}

completionHandlers.first?()
```

```text
Çıktı:
Closure daha sonra çağrılacak
```

Closure, fonksiyon bittikten sonra çağrıldığı için **@escaping** olarak işaretlenmelidir. Eğer **@escaping** kullanılmazsa, Swift derleyicisi hata verir.  

### Asenkron İşlemlerde Escaping Closure Kullanımı  

```swift
func fetchData(completion: @escaping (String) -> Void) {
    DispatchQueue.global().async {
        sleep(2) // Simüle edilmiş ağ isteği
        completion("Veri yüklendi")
    }
}

fetchData { result in
    print(result)
}
```

```text
Çıktı:
(2 saniye sonra)
Veri yüklendi
```
Burada **closure, `DispatchQueue` içinde asenkron çalıştığı için `@escaping` olarak tanımlanmalıdır.**  

Eğer **@escaping** yazılmazsa kod **derlenmez**: derleyici closure'ın fonksiyondan kaçtığını tespit eder ve derleme hatası verir (`Escaping closure captures non-escaping parameter 'completion'`). Bu bir çalışma anı (runtime) hatası değildir.

**Escaping closure’lar daha esnektir ancak bellek yönetimi açısından dikkatli kullanılmalıdır.**  

### Escaping Closure ve Retain Cycle (`[weak self]`)
Escaping closure, içinde kullandığı `self`'i güçlü (strong) olarak yakalar. `self` de bu closure'ı saklıyorsa retain cycle oluşur. Bunu önlemek için capture list kullanılır:

```swift
class ProfileViewModel {
    var name = "Ali"
    var onUpdate: (() -> Void)?

    func bind() {
        // Hatalı: onUpdate = { print(self.name) } → self closure'ı, closure da self'i tutar
        onUpdate = { [weak self] in
            guard let self else { return }
            print(self.name)
        }
    }
}
```

### async/await Alternatifi
Swift 5.5 ile gelen async/await, completion handler'lı escaping closure'ların yerini büyük ölçüde almıştır:

```swift
func fetchData() async -> String {
    try? await Task.sleep(for: .seconds(2)) // Simüle edilmiş ağ isteği
    return "Veri yüklendi"
}

Task {
    let result = await fetchData()
    print(result)
}
```


## Hangi Durumda Hangisi Kullanılmalı?  
### **Non-Escaping Closure** kullanılmalı:  
  - Closure, **fonksiyon çalışırken çağrılıyorsa** ve sonrasında unutuluyorsa.  
  - **Performans açısından daha güvenli** olduğu için varsayılan olarak tercih edilir.  

### **Escaping Closure** kullanılmalı:  
  - **Asenkron işlemler** (örneğin ağ istekleri, GCD kullanımı).  
  - **Tamamlanması gecikebilecek işlemler** (örneğin completion handler mekanizmaları).  
  - **Closure bir değişkene veya dizinin içine atanıyorsa.**  
---
# 7 - Array, Set, Dictionary Arasındaki Fark Nedir?

### Genel Tanım
Swift’te **Array, Set ve Dictionary**, en yaygın kullanılan koleksiyon türleridir. Her biri farklı veri yapıları ve kullanım senaryoları sunar.

- **Array:** Sıralı ve tekrarlı öğeler içeren bir koleksiyondur.
- **Set:** Tekrarlayan elemanları kabul etmeyen ve sıralama garantisi olmayan bir veri yapısıdır.
- **Dictionary:** Anahtar-değer çiftleri şeklinde veri saklayan bir yapıdır.

### Array
**Tanım:** Elemanları belirli bir sırayla tutan, tekrarlı değerlere izin veren bir koleksiyon türüdür.

**Özellikler:**
- Aynı türdeki elemanları sıralı şekilde tutar.
- Elemanlara **index numarası** ile erişilir (O(1)).
- Tekrarlayan değerleri saklayabilir.
- **Dizi içindeki elemanların sırası korunur.**
- Bir elemanın dizide olup olmadığını aramak (`contains`) O(n)'dir.

**Kullanım:**
```swift
var fruits: [String] = ["Elma", "Armut", "Muz", "Elma"]
print(fruits[0]) // "Elma"
```
### Set
**Tanım:** Elemanları sırasız ve tekrarsız olarak saklayan bir koleksiyon türüdür.

**Özellikler:**
- Aynı elemandan **yalnızca bir tane bulunabilir**.
- Elemanlar sırasız olarak tutulur.
- Elemanların `Hashable` protokolüne uyması gerekir.
- Array’e göre **arama işlemleri daha hızlıdır** (hash tabanlı, ortalama O(1)).

**Kullanım:**
```swift
var uniqueNumbers: Set<Int> = [1, 2, 3, 4, 1, 2]
print(uniqueNumbers) // [1, 2, 3, 4] (sıra rastgele olabilir)
```
### Dictionary
**Tanım:** Her elemanın **bir anahtara (key) ve değere (value)** sahip olduğu bir koleksiyon türüdür.

**Özellikler:**
- Anahtarlar **benzersiz** olmalıdır ve `Hashable` protokolüne uymalıdır.
- Her anahtar tek bir değere karşılık gelir; değerler tekrar edebilir.
- Anahtarla erişim hash tabanlıdır, **ortalama O(1)**.

**Kullanım:**
```swift
var studentGrades: [String: Int] = ["Ali": 90, "Veli": 85, "Ayşe": 95]
print(studentGrades["Ali"]) // Optional(90)
```

### Karşılaştırma Tablosu

| Özellik    | Array            | Set                | Dictionary       |
|------------|------------------|--------------------|-----------------|
| **Sıralama** | Evet            | Hayır              | Hayır           |
| **Tekrarlayan Elemanlar** | Evet | Hayır | Anahtarlar hayır, değerler evet |
| **Erişim Yöntemi** | Index ile | Eleman var mı kontrolü ile | Anahtar ile |
| **Index ile Erişim** | O(1) | Yok | Yok |
| **Arama (`contains` / anahtar)** | O(n) | Ortalama O(1) | Ortalama O(1) |
| **Gereksinim** | Yok | Element `Hashable` | Key `Hashable` |

## Hangi Durumda Hangisi Kullanılmalı?

- **Array** kullanılmalı:
  - **Sıralı** bir veri listesi gerektiğinde.
  - **Tekrarlayan** verileri saklamak istendiğinde.
  - **Dizi içindeki belirli bir konuma erişim gerekiyorsa.**

- **Set** kullanılmalı:
  - **Tekrarlayan verileri engellemek** istendiğinde.
  - **Hızlı arama ve karşılaştırma** gerekiyorsa.
  - **Eleman sırası önemli değilse.**

- **Dictionary** kullanılmalı:
  - **Verilere anahtar ile erişmek gerektiğinde.**
  - **Anahtara göre hızlı (ortalama O(1)) erişim gerektiğinde.**
  - **Her elemanın bir benzersiz anahtara sahip olması gerektiğinde.**

---

# 8 - High Order Functions Nelerdir?

### Genel Tanım
High Order Functions (Yüksek Mertebe Fonksiyonları), **diğer fonksiyonları parametre olarak alabilen veya dönüş değeri olarak fonksiyon döndürebilen fonksiyonlardır**. Swift dilinde, işlevsel programlama yaklaşımının bir parçası olarak yaygın şekilde kullanılırlar.

**Özellikler:**
- Daha az kod ile daha esnek işlemler yapmayı sağlar.
- Daha okunabilir ve modüler kod yazımını teşvik eder.
- Fonksiyonları birinci sınıf vatandaş olarak ele alır.

## Yaygın Kullanılan High Order Functions
### **map(_:)**
Bir koleksiyonun her elemanına belirli bir işlemi uygulayarak yeni bir koleksiyon döndürür.
```swift
let numbers = [1, 2, 3, 4]
let squaredNumbers = numbers.map { $0 * $0 }
print(squaredNumbers) // [1, 4, 9, 16]
```
### **compactMap(_:)**
`map` gibi çalışır, ancak sonucu `nil` olan elemanları atar ve optional'ları açar.
```swift
let values = ["1", "2", "abc", "4"]
let ints = values.compactMap { Int($0) }
print(ints) // [1, 2, 4]
```
### **flatMap(_:)**
Her eleman için bir koleksiyon üretir ve sonuçları tek bir düz koleksiyonda birleştirir.
```swift
let nested = [[1, 2], [3, 4], [5]]
let flat = nested.flatMap { $0 }
print(flat) // [1, 2, 3, 4, 5]
```
### **filter(_:)**
Belirtilen koşulu sağlayan elemanları içeren yeni bir koleksiyon döndürür.
```swift
let numbers = [1, 2, 3, 4, 5, 6]
let evenNumbers = numbers.filter { $0 % 2 == 0 }
print(evenNumbers) // [2, 4, 6]
```
### **reduce(_:_:)**
Koleksiyon elemanlarını tek bir değer haline getirir.
```swift
let numbers = [1, 2, 3, 4]
let sum = numbers.reduce(0, +)
print(sum) // 10
```
### **reduce(into:_:)**
Sonuç bir koleksiyon (dizi, sözlük) olduğunda her adımda kopya oluşturmadan, yerinde günceller; daha verimlidir.
```swift
let words = ["elma", "armut", "elma", "muz"]
let counts = words.reduce(into: [String: Int]()) { result, word in
    result[word, default: 0] += 1
}
print(counts) // ["elma": 2, "armut": 1, "muz": 1] (sıra değişebilir)
```
### **forEach(_:)**
Her eleman için belirli bir işlemi gerçekleştirir (ancak yeni bir koleksiyon döndürmez).
```swift
let names = ["Ali", "Ayşe", "Veli"]
names.forEach { print($0) }
```
**Dikkat:** `forEach` içinde `break` ve `continue` kullanılamaz. `return` yalnızca o elemanın closure'ından çıkar, yani `continue` gibi davranır; döngü durmaz. Döngüyü erken bitirmek gerekiyorsa `for-in` kullanılmalıdır.

### **sorted(by:)**
Koleksiyon elemanlarını belirtilen koşula göre sıralar.
```swift
let numbers = [5, 2, 8, 3]
let sortedNumbers = numbers.sorted(by: <)
print(sortedNumbers) // [2, 3, 5, 8]
```
### **first(where:), contains(where:), allSatisfy(_:)**
Koşula göre arama ve kontrol yapar; sonucu bulunca durur.
```swift
let numbers = [3, 7, 10, 15]
print(numbers.first(where: { $0 > 5 }) ?? -1)  // 7
print(numbers.contains(where: { $0 % 5 == 0 })) // true
print(numbers.allSatisfy { $0 > 0 })            // true
```
### **lazy**
Zincirlenen işlemleri hemen değil, sonuç istendiğinde ve yalnızca gerektiği kadar çalıştırır; ara diziler oluşturmaz.
```swift
let numbers = Array(1...1_000_000)
let firstBigSquare = numbers.lazy.map { $0 * $0 }.first { $0 > 1_000 }
print(firstBigSquare ?? 0) // 1024 (yalnızca 32 eleman işlenir)
```
## Hangi Durumda Kullanılmalı?
- **map:** Bir koleksiyonun her elemanına işlem uygulayıp yeni bir koleksiyon oluşturmak için.
- **compactMap:** Dönüşüm sonucu `nil` olabilecek durumlarda `nil`'leri ayıklamak için.
- **flatMap:** İç içe koleksiyonları düzleştirmek için.
- **filter:** Belirli bir koşulu sağlayan elemanları almak için.
- **reduce / reduce(into:):** Bir koleksiyonun tüm elemanlarını tek bir değere (veya yeni bir koleksiyona) indirgemek için.
- **forEach:** Koleksiyon elemanları üzerinde yan etki oluşturarak döngü çalıştırmak için.
- **sorted:** Elemanları belirli bir sıralama ile düzenlemek için.
- **first(where:) / contains(where:) / allSatisfy:** Koşula göre arama ve kontrol için.
- **lazy:** Büyük koleksiyonlarda zincirleme işlemleri gereksiz ara diziler oluşturmadan yapmak için.
---

# 9 - ARC (Automatic Reference Counting) Nedir? Ne İşe Yarar?

### Genel Tanım
ARC (Automatic Reference Counting), Swift dilinde bellek yönetimini otomatik olarak gerçekleştiren bir sistemdir. ARC, class instance'larının bellekten ne zaman kaldırılacağını belirlemek için **referans sayacı** kullanır. **Gereksiz bellek kullanımı ve bellek sızıntılarını önlemek için tasarlanmıştır.**

Swift, geliştiricinin manuel olarak bellek yönetimi yapmasını gerektirmeden ARC sayesinde **otomatik olarak** bellek tahsisini ve serbest bırakılmasını gerçekleştirir.

ARC bir **garbage collector değildir**: arka planda periyodik çalışan bir toplayıcı yoktur. Retain/release çağrılarını **derleyici, derleme zamanında** koda ekler; nesne, referans sayısı sıfıra düştüğü anda serbest bırakılır.

## ARC Nasıl Çalışır?

- Bir nesne oluşturulduğunda, ARC bu nesneye **bir referans sayacı (reference count)** atar.
- Başka bir nesne bu nesneye referans verdiğinde, referans sayacı **arttırılır**.
- Eğer referansı tutan değişkenler silinirse veya nil atanırsa, referans sayacı **azalır**.
- Referans sayacı **0 olduğunda**, ARC nesneyi bellekten otomatik olarak kaldırır.


### Örnek Kullanım
```swift
class Person {
    let name: String
    
    init(name: String) {
        self.name = name
        print("\(name) oluşturuldu")
    }
    
    deinit {
        print("\(name) bellekten kaldırıldı")
    }
}

var person1: Person? = Person(name: "Ali")
person1 = nil // ARC referans sayısını 0 yapar ve nesneyi bellekten kaldırır
```
**Çıktı:**
```text
Ali oluşturuldu
Ali bellekten kaldırıldı
```
Bu örnekte `person1` nesnesi **nil atanınca**, referans sayısı **0** olduğu için bellekten kaldırılmıştır.

## ARC ve Referans Tipleri

### **Strong Reference (Güçlü Referans)**
Varsayılan referans tipidir. Bir nesneye güçlü bir referans olduğunda, nesne bellekten kaldırılamaz.
```swift
class Car {
    let model: String
    init(model: String) {
        self.model = model
    }
}
var car1: Car? = Car(model: "Tesla") // Referans sayısı: 1
var car2 = car1 // Referans sayısı: 2
car1 = nil // Referans sayısı: 1
car2 = nil // Referans sayısı: 0, nesne bellekten kaldırılır
```

### **Weak Reference (Zayıf Referans)**
Güçlü döngüler (retain cycle) oluşmasını önlemek için `weak` kullanılır. Weak referans, referans sayacını **arttırmaz**. Referans verilen nesne bellekten kaldırıldığında weak referans otomatik olarak **nil** olur; bu yüzden her zaman optional bir `var` olmalıdır.
```swift
class Employee {
    var name: String
    weak var department: Department? // Çalışan departmana sahip değil → weak
    init(name: String) {
        self.name = name
    }
}

class Department {
    var title: String
    var manager: Employee? // Departman yöneticisine sahip → strong
    init(title: String) {
        self.title = title
    }
}

var employee: Employee? = Employee(name: "Mehmet")
var department: Department? = Department(title: "IT")

department?.manager = employee   // strong
employee?.department = department // weak → iki yönlü bağ var ama retain cycle oluşmaz

department = nil // Department bellekten kaldırılır, employee?.department otomatik nil olur
```

### **Unowned Reference (Sahipsiz Referans)**
`unowned` da weak gibi referans sayacını arttırmaz, ancak **optional değildir ve nesne silindiğinde nil olmaz**. Referans verilen nesne bellekten kaldırıldıktan sonra unowned referansa erişilirse uygulama **çöker**. Bu yüzden yalnızca referans verilen nesnenin ömrünün, referansı tutan nesnenin ömrü kadar veya ondan uzun olduğundan emin olunan durumlarda kullanılır.
```swift
class Customer {
    let name: String
    var card: CreditCard? // Müşteri kartına sahip (strong)
    init(name: String) {
        self.name = name
    }
    deinit { print("\(name) bellekten kaldırıldı") }
}

class CreditCard {
    let number: String
    unowned let customer: Customer // Kart, sahibinden daha uzun yaşamaz
    init(number: String, customer: Customer) {
        self.number = number
        self.customer = customer
    }
    deinit { print("Kart \(number) bellekten kaldırıldı") }
}

var ali: Customer? = Customer(name: "Ali")
ali!.card = CreditCard(number: "1234", customer: ali!)
ali = nil
```
**Çıktı:**
```text
Ali bellekten kaldırıldı
Kart 1234 bellekten kaldırıldı
```
Bu örnekte kartın sahibi olmadan var olamayacağı kesin olduğu için `unowned` uygundur. Müşteri silindikten sonra kart bir yerde tutulup `card.customer`'a erişilseydi uygulama çökerdi.

## Retain Cycle (Referans Döngüsü) ve ARC

ARC, nesnelerin referans sayısını takip etse de, **retain cycle (referans döngüsü)** oluşursa bellekten temizlenmez. Retain cycle oluştuğunda **weak veya unowned** referanslar kullanılmalıdır.

### **Retain Cycle Örneği (Hata)**
```swift
class Person {
    var name: String
    var pet: Pet?
    init(name: String) {
        self.name = name
    }
}

class Pet {
    var type: String
    var owner: Person?
    init(type: String) {
        self.type = type
    }
}

var owner: Person? = Person(name: "Ahmet")
var pet: Pet? = Pet(type: "Köpek")
owner?.pet = pet
pet?.owner = owner // Retain cycle oluşur!
```
Bu durumda, `owner` ve `pet` birbirine **strong reference** ile bağlı olduğu için **referans sayıları asla sıfırlanmaz** ve nesneler bellekten silinmez. Bunu önlemek için `weak` veya `unowned` referanslar kullanılmalıdır.

### **Retain Cycle Çözümü (Weak Kullanımı)**
```swift
class Person {
    var name: String
    var pet: Pet? // Kişi evcil hayvanına sahip → strong
    init(name: String) {
        self.name = name
    }
}

class Pet {
    var type: String
    weak var owner: Person? // Geri referans → weak
    init(type: String) {
        self.type = type
    }
}
```
Bu çözümde `weak` referans, sahiplenilen taraftaki **geri referansa** (`Pet.owner`) konduğu için retain cycle oluşmaz. `Person.pet`'i weak yapmak döngüyü kırsa da yanlış olurdu: evcil hayvanı strong tutan başka kimse olmadığında hemen bellekten silinirdi.

### **Closure'larda Retain Cycle**
Bir nesne bir closure'ı saklıyor ve closure da `self`'i yakalıyorsa retain cycle oluşur. Çözüm capture list kullanmaktır:
```swift
class TimerViewModel {
    var onTick: (() -> Void)?
    var count = 0

    func start() {
        onTick = { [weak self] in   // veya ömür garantiliyse [unowned self]
            self?.count += 1
        }
    }

    deinit { print("TimerViewModel bellekten kaldırıldı") }
}
```

## Sonuç

- **ARC**, Swift’te bellek yönetimini otomatik hale getirerek manuel yönetim ihtiyacını ortadan kaldırır; bir garbage collector değildir, retain/release çağrılarını derleyici ekler.
- **Referans sayacı** ile nesnelerin ne zaman bellekten silineceğini belirler.
- **Retain cycle** oluşmasını önlemek için **weak ve unowned referanslar** (closure'larda `[weak self]` / `[unowned self]`) kullanılmalıdır.
- Memory leak'ler Xcode'daki **Memory Graph Debugger** ve Instruments'taki **Leaks** aracıyla bulunabilir; `deinit`'in çağrılmaması da bir leak işaretidir.
- **ARC sayesinde daha verimli ve güvenli bellek yönetimi sağlanır.**
---

# 10 - Optional ve Unwrap Nedir? Ne İşe Yarar?

### Genel Tanım
Swift dilinde **Optional**, bir değişkenin **değer içerebileceğini veya nil (boş) olabileceğini** belirtmek için kullanılan bir veri türüdür. Optional'lar, **runtime sırasında nil hatalarından kaçınmak ve daha güvenli kod yazmak** için kullanılır.

Bir değişkenin nil olabileceğini göstermek için `?` işareti kullanılır.

```swift
var name: String? = "Ali"
print(name) // Optional("Ali")
```
Bu değişkenin değeri olabilir veya olmayabilir. Eğer bir optional değişkeni **unwrap (açmak)** istiyorsak, birkaç farklı yöntem kullanabiliriz.

### Optional Aslında Bir Enum'dır
`String?` yazımı, `Optional<String>` tipinin kısaltmasıdır. Optional, iki case'i olan generic bir enum'dır:

```swift
enum Optional<Wrapped> {
    case none           // nil
    case some(Wrapped)  // değer var
}

let age: Int? = 30
switch age {
case .some(let value): print("Yaş: \(value)")
case .none: print("Yaş yok")
}
```

## Optional Değerleri Unwrap Etme Yöntemleri

### 1. **Forced Unwrapping (`!`)**
Eğer bir optional değişkenin **kesinlikle bir değere sahip olduğunu biliyorsak**, `!` kullanarak onu unwrap edebiliriz. Ancak, eğer değer nil ise uygulama **çöker**.

```swift
var name: String? = "Ali"
print(name!) // "Ali"
```
**Tehlike:** Eğer `name` nil olursa, program çöker:
```swift
var name: String? = nil
print(name!) // Runtime error!
```

### 2. **Optional Binding (`if let` veya `guard let`)**
Bir optional değişkenin nil olup olmadığını güvenli şekilde kontrol ederek unwrap etmek için **if let** veya **guard let** kullanılır.

#### **if let Kullanımı**
```swift
var name: String? = "Ali"
if let unwrappedName = name {
    print("Merhaba, \(unwrappedName)")
} else {
    print("İsim bulunamadı.")
}

// Swift 5.7+ kısa yazım: aynı isimle unwrap
if let name {
    print("Merhaba, \(name)")
}
```

#### **guard let Kullanımı**
Guard, özellikle fonksiyonlar içinde erken çıkış yapmak için kullanılır.
```swift
func greet(_ name: String?) {
    guard let unwrappedName = name else {
        print("İsim bulunamadı.")
        return
    }
    print("Merhaba, \(unwrappedName)")
}
greet("Ali") // "Merhaba, Ali"
greet(nil)  // "İsim bulunamadı."
```

### 3. **Nil-Coalescing Operator (`??`)**
Eğer optional değişken nil ise, yerine **varsayılan bir değer** atamak için kullanılır.

```swift
var name: String? = nil
print(name ?? "Bilinmeyen") // "Bilinmeyen"
```

### 4. **Optional Chaining (`?.`)**
Optional değerlerin iç içe geçmiş (nested) nesnelere güvenli şekilde erişmesini sağlar.

```swift
class Person {
    var pet: String?
}
let person = Person()
print(person.pet?.uppercased() ?? "Evcil hayvan yok")
```
Eğer `person.pet` nil ise kod çökmez; optional chaining zincirin geri kalanını çalıştırmadan **nil döner**. Ekrandaki varsayılan değeri ("Evcil hayvan yok") optional chaining değil, `??` operatörü sağlar.

### 5. **Optional Üzerinde `map` / `flatMap`**
Optional'ı açmadan, değer varsa dönüştürmek için kullanılır; değer yoksa sonuç nil olur.

```swift
let input: String? = "42"
let number = input.flatMap { Int($0) } // Optional(42)
let doubled = number.map { $0 * 2 }    // Optional(84)
```

### 6. **`try?`**
Hata fırlatabilen bir ifadenin sonucunu optional'a çevirir; hata olursa nil döner.

```swift
let data = try? Data(contentsOf: fileURL) // Hata olursa nil
```

### 7. **Implicitly Unwrapped Optional (`!` ile tanımlama)**
`String!` şeklinde tanımlanan değişken optional'dır, ancak kullanıldığı yerde otomatik olarak force unwrap edilir. Nil iken erişilirse uygulama çöker. Genellikle `@IBOutlet` gibi, ilk kullanımdan önce mutlaka atanacağı bilinen değerlerde kullanılır.

```swift
@IBOutlet weak var titleLabel: UILabel!
var token: String! = nil
// print(token.count) // Runtime error! (nil iken erişildi)
```

## Hangi Durumda Hangisi Kullanılmalı?
| Yöntem | Kullanım Amacı |
|--------|---------------|
| **Forced Unwrapping (!)** | Değişkenin kesinlikle nil olmayacağından eminsek. |
| **Optional Binding (if let, guard let)** | Değişkenin nil olup olmadığını güvenli şekilde kontrol etmek için. |
| **Nil-Coalescing Operator (??)** | Nil olursa varsayılan bir değer kullanmak için. |
| **Optional Chaining (?.)** | İç içe geçmiş optional değerleri güvenli şekilde çağırmak için (nil ise nil döner). |
| **map / flatMap** | Optional'ı açmadan dönüştürmek için. |
| **try?** | Hatayı önemsemeyip sonucu optional olarak almak için. |
| **Implicitly Unwrapped Optional (Tip!)** | İlk kullanımdan önce kesin atanacak değerler için (ör. `@IBOutlet`). |

## Sonuç
- **Optional**, nil hatalarını önleyerek daha güvenli kod yazmayı sağlar; arka planda `.some` / `.none` case'leri olan bir enum'dır.
- **Forced unwrapping (`!`) ve implicitly unwrapped optional dikkatli kullanılmalıdır, aksi halde uygulama çökebilir.**
- **Güvenli unwrap yöntemleri (if let, guard let, ??) tercih edilmelidir.**
- **Optional chaining, nested değerlerde güvenli erişim sağlar.**
---

# 11 - Access Level (Erişim Seviyeleri) Nedir?

### Genel Tanım
Swift'te **Access Level (Erişim Seviyeleri)**, bir kodun **hangi bileşenlere erişebileceğini** belirleyen mekanizmadır. Bu sistem, kodun daha modüler ve güvenli olmasını sağlar. Swift’te **altı farklı erişim seviyesi** vardır:

1. **open** – En geniş erişim seviyesi; başka modüllerden erişilebilir, subclass ve override edilebilir.
2. **public** – Başka modüllerden erişilebilir (okuma/yazma kısıtı yoktur); ancak başka modüllerde subclass ve override **edilemez**. Aynı modül içinde subclass edilebilir.
3. **package** – (Swift 5.9+) Aynı Swift paketindeki tüm modüllerden erişilebilir, paket dışından erişilemez.
4. **internal** – Varsayılan erişim seviyesi, aynı modül içinde erişime izin verir.
5. **fileprivate** – Aynı dosya içinde erişime izin verir.
6. **private** – Tanımlandığı kapsam (ör. type) ve aynı dosyadaki o türün extension'ları içinde erişilebilir.

## Access Level Türleri
### 1. **Open ve Public**
- `open` ve `public`, en geniş erişim seviyeleridir ve **modüller (framework’ler) arasında kullanılabilirler**.
- `open`, başka modüllerde miras almayı (subclassing) ve override etmeyi destekler. `public` ise başka modüllerde **subclass ve override'ı engeller**; okuma/yazma ile ilgili bir kısıt getirmez.

```swift
open class Animal {
    public var name: String

    public init(name: String) { // init de public olmalı, aksi halde başka modüller nesne oluşturamaz
        self.name = name
    }

    open func makeSound() {
        print("Animal sound")
    }
}
```
**Ne zaman kullanılır?**
- `open`: Kütüphanelerde ve başkalarının genişletmesini istediğimiz sınıflarda.
- `public`: Kütüphanelerde ancak **başka modüllerde miras alınmasını istemediğimiz** sınıflarda.

### 2. **Package**
- Swift 5.9 ile geldi. Aynı Swift paketi (SPM package) içindeki **farklı modüller** birbirinin `package` tanımlarını görebilir; paketi kullanan dış projeler göremez.

```swift
package struct NetworkConfig {
    package var baseURL: String
}
```
**Ne zaman kullanılır?**
- Bir paketi birden fazla modüle böldüğünüzde, modüller arası paylaşılan ama dışarıya açılmaması gereken kod için.

### 3. **Internal (Varsayılan)**
- **Varsayılan erişim seviyesi** olup, aynı modül içindeki kod tarafından erişilebilir.
- Swift dosyaları **aynı hedef (target) içinde olduğu sürece** birbirine erişebilir.

```swift
internal class User {
    internal var username: String = "admin"
}
```
**Ne zaman kullanılır?**
- Kendi modülümüz içinde kullanacak bileşenlerde.
- **Modüler yapı oluştururken** dış erişimi kısıtlamak için.

### 4. **Fileprivate**
- **Aynı dosya içindeki kod tarafından erişilebilir**.
- Başka bir dosyadan erişilemez.

```swift
fileprivate class DatabaseConnection {
    fileprivate func connect() {
        print("Bağlantı kuruldu")
    }
}
```
**Ne zaman kullanılır?**
- **Aynı dosya içindeki sınıflar arasında veri paylaşımı gerektiğinde.**
- **Dosya bazlı kapsülleme için.**

### 5. **Private**
- **Yalnızca tanımlandığı kapsam içinde** (ör. sınıfın gövdesi) erişilebilir.
- Swift 4'ten beri aynı türün **aynı dosyadaki extension'larından** da erişilebilir.
- Sınıfın kendi metotları ve property'leri private üyelere erişebilir; başka türler ve başka dosyalar erişemez.

```swift
class Person {
    private var id: String = "12345"
    
    private func generateID() {
        self.id = UUID().uuidString
    }
}

extension Person { // Aynı dosyada
    func resetID() {
        generateID() // ✅ private üyeye erişebilir
    }
}
```
**Ne zaman kullanılır?**
- **Gerçekten gizli olması gereken veriler için.**
- **Başka hiçbir bileşenin erişmesini istemediğimiz özellikler için.**

### `private(set)` ile Yalnızca Yazmayı Kısıtlamak
Getter ve setter'a farklı erişim seviyesi verilebilir. Örneğin dışarıdan okunabilen ama yalnızca türün kendisi tarafından değiştirilebilen bir property:

```swift
class Counter {
    private(set) var count = 0 // Okuma internal, yazma private

    func increment() {
        count += 1
    }
}

let counter = Counter()
counter.increment()
print(counter.count) // 1
// counter.count = 5 // Derleme hatası: setter private
```

## Karşılaştırma Tablosu

| Access Level   | Aynı Sınıf | Aynı Dosya | Aynı Modül | Başka Modüller |
|---------------|-----------|------------|------------|--------------|
| **open**      | ✅        | ✅         | ✅         | ✅ (Miras alabilir) |
| **public**    | ✅        | ✅         | ✅         | ✅ (Miras alınamaz) |
| **package**   | ✅        | ✅         | ✅         | ⚠️ Yalnızca aynı paketteki modüller |
| **internal**  | ✅        | ✅         | ✅         | ❌ |
| **fileprivate** | ✅       | ✅         | ❌         | ❌ |
| **private**   | ✅        | ⚠️ Yalnızca aynı türün aynı dosyadaki extension'ları | ❌         | ❌ |

## Hangi Durumda Hangisi Kullanılmalı?

- **open/public**: Başka modüllerden erişime açık olması gereken sınıflar için.
- **package**: Aynı paketin modülleri arasında paylaşılıp dışarıya kapalı kalması gereken kod için.
- **internal**: Varsayılan olarak kullanılır, modül içindeki genel kullanım için uygundur.
- **fileprivate**: Aynı dosya içindeki kod arasında veri paylaşımı için uygundur.
- **private**: Yalnızca aynı kapsam içinde erişilmesi gereken veriler için kullanılır.
---

# 12 - SOLID Prensipleri

### Genel Tanım
SOLID, ilk olarak **nesne yönelimli programlama (OOP)** için ortaya konmuş beş temel prensibi ifade eden bir kısaltmadır. Swift'te protokollerle (POP) de birebir uygulanır; aşağıdaki örneklerin çoğu da protokol kullanır. Bu prensipler, kodun **daha esnek, sürdürülebilir ve yönetilebilir** olmasını sağlar.

SOLID’in açılımı:
1. **S** - Single Responsibility Principle (Tek Sorumluluk İlkesi)
2. **O** - Open/Closed Principle (Açık/Kapalı İlkesi)
3. **L** - Liskov Substitution Principle (Liskov Yerine Koyma İlkesi)
4. **I** - Interface Segregation Principle (Arayüz Ayrımı İlkesi)
5. **D** - Dependency Inversion Principle (Bağımlılıkları Ters Çevirme İlkesi)

### 1. Single Responsibility Principle (Tek Sorumluluk İlkesi)
**Bir sınıfın yalnızca tek bir sorumluluğu olmalıdır.**

Bir sınıfın **yalnızca bir amaç için değiştirilmesi gerektiğini** belirten bu ilke, **bağımsız ve modüler bir yapı** oluşturmayı hedefler.

**Yanlış Kullanım:**
```swift
class Report {
    func generateReport() {
        print("Rapor oluşturuldu")
    }
    func saveToFile() {
        print("Dosyaya kaydedildi")
    }
}
```
Bu sınıf **rapor oluşturma ve kaydetme** işlerini aynı anda yapıyor. Bunlar **farklı sorumluluklardır**.

**Doğru Kullanım:**
```swift
class ReportGenerator {
    func generateReport() {
        print("Rapor oluşturuldu")
    }
}

class ReportSaver {
    func saveToFile() {
        print("Dosyaya kaydedildi")
    }
}
```
Bu şekilde sınıflar, **yalnızca kendi sorumluluklarını üstlenir**.

### 2. Open/Closed Principle (Açık/Kapalı İlkesi)
**Bir sınıf genişlemeye açık, değişikliğe kapalı olmalıdır.**

Yeni özellikler eklemek için mevcut sınıfları değiştirmek yerine, **genişletilebilir yapılar (extension, inheritance, protocol) kullanılmalıdır.**

**Yanlış Kullanım:**
```swift
class Discount {
    func applyDiscount(amount: Double, type: String) -> Double {
        if type == "student" {
            return amount * 0.9
        } else if type == "vip" {
            return amount * 0.8
        }
        return amount
    }
}
```
Yeni bir indirim türü eklemek için bu sınıfı **sürekli değiştirmek gerekir**, bu da ilkeye aykırıdır.

**Doğru Kullanım:**
```swift
protocol Discount {
    func apply(amount: Double) -> Double
}

class StudentDiscount: Discount {
    func apply(amount: Double) -> Double {
        return amount * 0.9
    }
}

class VIPDiscount: Discount {
    func apply(amount: Double) -> Double {
        return amount * 0.8
    }
}
```
Bu sayede **yeni indirim türleri eklerken var olan kodu değiştirmek zorunda kalmayız.**

### 3. Liskov Substitution Principle (Liskov Yerine Koyma İlkesi)
**Bir alt sınıf, üst sınıfın yerine kullanılabilmelidir.**

Bu ilke, **kalıtımın yanlış kullanımını önler**. Eğer bir alt sınıf, üst sınıf yerine kullanıldığında beklenmeyen davranışlar sergiliyorsa, bu ilke ihlal edilmiştir.

**Yanlış Kullanım:**
```swift
class Bird {
    func fly() {
        print("Uçuyor")
    }
}

class Penguin: Bird {
    override func fly() {
        fatalError("Penguenler uçamaz!")
    }
}
```
Penguen sınıfı, kuş sınıfının yerine kullanılamaz çünkü **uçma metodunu geçersiz kılıyor**.

**Doğru Kullanım:**
```swift
protocol Flyable {
    func fly()
}

class Bird {}

class Sparrow: Bird, Flyable {
    func fly() {
        print("Serçe uçuyor")
    }
}

class Penguin: Bird {}
```
Bu şekilde **Penguen sınıfı `fly()` metodunu içermediği için yanlış kullanım engellenmiş olur.**

### 4. Interface Segregation Principle (Arayüz Ayrımı İlkesi)
**Büyük ve genel arayüzler yerine, spesifik ve daha küçük arayüzler oluşturulmalıdır.**

**Yanlış Kullanım:**
```swift
protocol Worker {
    func work()
    func eat()
}

class Robot: Worker {
    func work() {
        print("Robot çalışıyor")
    }
    func eat() {
        fatalError("Robot yemek yiyemez")
    }
}
```
Bu durumda `Robot` sınıfı, **kullanmadığı bir metodu implement etmek zorunda kalıyor.**

**Doğru Kullanım:**
```swift
protocol Workable {
    func work()
}

protocol Eatable {
    func eat()
}

class Human: Workable, Eatable {
    func work() {
        print("İnsan çalışıyor")
    }
    func eat() {
        print("İnsan yemek yiyor")
    }
}

class Robot: Workable {
    func work() {
        print("Robot çalışıyor")
    }
}
```
Bu sayede **her sınıf yalnızca ihtiyacı olan metotları içerir**.

### 5. Dependency Inversion Principle (Bağımlılıkları Ters Çevirme İlkesi)
**Üst seviye modüller alt seviye modüllere bağımlı olmamalıdır; her ikisi de soyutlamalara (protocol) bağımlı olmalıdır. Soyutlamalar detaylara değil, detaylar soyutlamalara bağlı olmalıdır.**

**Yanlış Kullanım:**
```swift
class MySQLDatabase {
    func fetchData() {
        print("MySQL verisi çekildi")
    }
}

class DataManager {
    let database = MySQLDatabase()
    func loadData() {
        database.fetchData()
    }
}
```
Burada `DataManager` doğrudan `MySQLDatabase` sınıfına bağımlıdır. Eğer farklı bir veritabanına geçmek istersek, `DataManager` sınıfını değiştirmemiz gerekir.

**Doğru Kullanım:**
```swift
protocol Database {
    func fetchData()
}

class MySQLDatabase: Database {
    func fetchData() {
        print("MySQL verisi çekildi")
    }
}

class DataManager {
    let database: Database
    
    init(database: Database) {
        self.database = database
    }
    
    func loadData() {
        database.fetchData()
    }
}
```
Bu sayede **farklı bir veritabanı kullanmak istediğimizde, `DataManager` sınıfını değiştirmemize gerek kalmaz**. Hem `DataManager` (üst seviye) hem `MySQLDatabase` (alt seviye) artık `Database` soyutlamasına bağlıdır; test sırasında da kolayca bir mock verilebilir.
### Sonuç
SOLID prensipleri, daha sürdürülebilir, genişletilebilir ve hatasız bir yazılım geliştirmeyi sağlar. Bu kuralları takip ederek kodunuzu daha okunabilir, yönetilebilir ve esnek hale getirebilirsiniz.

---


## Şirketler
Şirketlerin işe alım süreci ile ilgili detaylar.

### Trendyol

### Appcent
