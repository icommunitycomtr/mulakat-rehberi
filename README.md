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
- [9 - ARC (Automatic Reference Counting) Nedir? Ne İşe Yarar?](#9---arc-automatic-reference-counting-nedir-ne-işe-yarar)
- [10 - Optional ve Unwrap Nedir? Ne İşe Yarar?](#10---optional-ve-unwrap-nedir-ne-işe-yarar)
- [11 - Access Level (Erişim Seviyeleri) Nedir?](#11---access-level-erişim-seviyeleri-nedir)
- [12 - SOLID Prensipleri](#12---solid-prensipleri)


# 1 - Class ve Struct’ın Farkları

### Genel Tanım
Swift programlama dilinde **Class (Sınıf)** ve **Struct (Yapı)**, veri modelleri oluşturmak için kullanılan iki temel yapıdır. Her ikisi de **özellik (property)** ve **metot (method)** tanımlayabilir, ancak çalışma şekilleri açısından önemli farklılıklar içerir.

- **Class (Sınıf)**, referans tipidir (**Reference Type**). Bellekte **heap** üzerinde tutulur ve nesneler arasında referans olarak paylaşılır. Aynı nesne farklı değişkenler tarafından paylaşılabilir, bu da **paylaşılan değişikliklere (shared mutation)** olanak tanır.
- **Struct (Yapı)**, değer tipidir (**Value Type**). **Stack** üzerinde saklanır ve her atamada kopyalanarak yeni bir bağımsız örnek oluşturulur. Bu, **immutable (değişmez) ve thread-safe** yapıların oluşturulmasını kolaylaştırır.

Swift, özellikle **Struct** kullanımını teşvik eder, çünkü Struct'lar **daha güvenli, daha performanslı ve thread-safe** çalışır. Ancak, **Class** kullanımının da gerekli olduğu bazı durumlar vardır.

### Temel Farklar

| Özellik                | Class                     | Struct                     |
|------------------------|--------------------------|----------------------------|
| **Referans Tipi**      | Evet (Reference Type)  | Hayır (Value Type)      |
| **Bellek Yönetimi**    | Heap üzerinden yönetilir | Stack üzerinden yönetilir  |
| **Kopyalama Davranışı**| Referans üzerinden paylaşılır (Shared Reference) | Değer olarak kopyalanır (Copy-on-Write) |
| **Inheritance (Kalıtım)** | Destekler       | Desteklemez            |
| **Mutability (Değiştirilebilirlik)** | `var` ile değiştirilebilir | `mutating` anahtar kelimesi gerekir |
| **ARC (Automatic Reference Counting)** | Evet | Hayır |
| **Deinitializers** | `deinit` metodu vardır | Deinitializer kullanamaz |

### Class Kullanımı

````swift
class Person {
    var name: String

    init(name: String) {
        self.name = name
    }
}

let person1 = Person(name: "Ali")
let person2 = person1
person2.name = "Veli"

print(person1.name) // "Veli" (Referans tip olduğu için aynı nesne değiştirildi.)
````

### Struct Kullanımı

````swift
struct Person {
    var name: String
}

var person1 = Person(name: "Ali")
var person2 = person1
person2.name = "Veli"

print(person1.name) // "Ali" (Kopya oluşturuldu, orijinal veri değişmedi.)
````

## Hangi Durumda Hangisi Kullanılmalı?
### Struct Kullanımı Tercih Edilmeli
- Küçük ve hafif veri tipleri için (örneğin `CGPoint`, `CGSize`, `CGRect`).
- Immutable (değişmez) veriler için.
- Kopyalama işlemlerinin maliyetinin düşük olduğu durumlarda.
- Thread-safe (çoklu iş parçacığında güvenli) bir veri yapısı gerektiğinde.

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
| **Miras (Inheritance)**      | Tekli kalıtım (single inheritance) destekler | Çoklu protokol uyumu mümkündür |
| **Çoklu Kalıtım**            | Desteklenmez (Class'lar tek bir sınıftan türetilir) | Evet, birden fazla protokol kullanılabilir |
| **Bellek Yönetimi**          | Class’lar heap üzerinde tutulur (ARC ile yönetilir) | Struct ve enum kullanımı teşvik edilir (stack belleğinde daha verimli) |
| **Kod Yeniden Kullanımı**    | Alt sınıflar (subclass) ile sağlanır | Protokol genişletmeleri (protocol extensions) ile sağlanır |
| **Esneklik**                 | Katı ve hiyerarşik bir yapı vardır | Daha modüler ve genişletilebilir yapı sunar |
| **Test Edilebilirlik**       | Bağımlılıklar fazla olabilir, test edilmesi zorlaşır | Test etmek ve mock'lamak daha kolaydır |

### OOP Kullanımı (Class Tabanlı Miras)

````swift
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
````

### POP Kullanımı (Protocol Tabanlı Tasarım)

````swift
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
````
## Hangi Durumda Hangisi Kullanılmalı?
### OOP Kullanımı Tercih Edilmeli:
- Büyük ve karmaşık nesne yapılarında.
- UIKit gibi class tabanlı framework’lerle çalışırken.
- Referans tipli nesnelerin paylaşılması gerekiyorsa.
- Nesneler arasında miras ve davranış paylaşımı önemliyse.

### POP Kullanımı Tercih Edilmeli:
- Değer tipi (struct, enum) kullanımı gerekiyorsa.
- Kodun daha modüler ve test edilebilir olması isteniyorsa.
- Çoklu kalıtım ihtiyacı varsa.
- Esnek ve genişletilebilir bir yapı oluşturulması isteniyorsa.
---

# 3 - Uygulamanın Yaşam Döngüsü (App Lifecycle)

### Genel Tanım
iOS uygulamalarında, sistem tarafından yönetilen ve uygulamanın **arka plana alınması, kapatılması veya yeniden başlatılması** gibi durumları yöneten bir yaşam döngüsü mekanizması bulunur. 

**UIApplicationDelegate ve UISceneDelegate** protokolleri sayesinde, uygulamanın bu yaşam döngüsündeki önemli olayları yakalayarak uygun işlemleri gerçekleştirebiliriz.

### Uygulama Yaşam Döngüsü Aşamaları

| Aşama               | Açıklama |
|---------------------|------------------------------------------------------------|
| **Not Running**     | Uygulama başlatılmamış veya kapatılmış durumda.           |
| **Inactive**        | Uygulama ön planda ancak kullanıcı etkileşimde değil.     |
| **Active**          | Uygulama ön planda ve kullanıcı ile etkileşimde.          |
| **Background**      | Uygulama arka planda çalışıyor, ancak aktif olarak görünmüyor. |
| **Suspended**       | Uygulama bellekte tutuluyor ama işlem yapmıyor.           |

## Uygulama Yaşam Döngüsü Metotları
### UIApplicationDelegate ile Yönetim (iOS 12 ve Öncesi)
````swift
@UIApplicationMain
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
        print("Uygulama kapatılıyor.")
    }
}
````

### UISceneDelegate ile Yönetim (iOS 13 ve Sonrası)
````swift
class SceneDelegate: UIResponder, UIWindowSceneDelegate {

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
````

## Uygulama Yaşam Döngüsünde Yapılabilecek İşlemler
### `didFinishLaunchingWithOptions`
- Uygulama başlatıldığında gerekli başlangıç ayarlarını yap.
- Push bildirimlerini kontrol et.
- Veri tabanı bağlantısını başlat.

### `applicationWillResignActive`
- Kullanıcı çağrı aldıysa veya ekran kilitlendiyse çalışır.
- Aktif olmayan durumlarda, oyunları duraklat veya medya oynatmayı durdur.

### `applicationDidEnterBackground`
- Kullanıcının son verilerini kaydet.
- Açık ağ bağlantılarını kapat ve CPU tüketimini azalt.
- Arka planda çalışacak işlemleri (örn. müzik çalma) yönet.

### `applicationWillEnterForeground`
- Uygulama yeniden ön plana dönerken animasyonlar veya UI güncellemeleri yap.
- Kullanıcının bıraktığı yerden devam etmesini sağla.

### `applicationDidBecomeActive`
- Kullanıcı uygulamaya geri döndüğünde medya oynatmayı başlat veya güncellemeleri kontrol et.

### `applicationWillTerminate`
- Uygulama tamamen kapanmadan önce dosya kayıt işlemleri gerçekleştir.
- Kullanıcının durumunu kaydet ve çıkış işlemlerini yap.
---

# 4 - ViewController’ın Yaşam Döngüsü (View Lifecycle)

### Genel Tanım
iOS'ta **UIViewController**, bir ekranın veya bileşenin yaşam döngüsünü yöneten temel sınıftır. Her **ViewController**, **UIKit** tarafından belirli bir sırayla çağrılan metodlara sahiptir ve bu metotlar, ekranın yüklenmesi, görünmesi, güncellenmesi ve yok edilmesi gibi süreçleri kontrol etmemizi sağlar.

ViewController yaşam döngüsünü anlamak, **UI güncellemelerini optimize etmek, bellek yönetimini iyileştirmek ve performansı artırmak** için önemlidir.

### ViewController Yaşam Döngüsü Aşamaları

| Metot                  | Açıklama |
|------------------------|------------------------------------------------------------|
| `loadView()`           | View oluşturulurken çağrılır. Manuel olarak değiştirilmedikçe genellikle dokunulmaz. |
| `viewDidLoad()`        | View yüklenip hafızaya alındığında çağrılır. UI ayarları ve veri yükleme burada yapılır. |
| `viewWillAppear()`     | View ekrana gelmeden hemen önce çağrılır. UI güncellemeleri ve animasyon hazırlıkları burada yapılır. |
| `viewDidAppear()`      | View ekrana geldikten sonra çağrılır. Kullanıcı etkileşimleri burada başlatılır. |
| `viewWillDisappear()`  | View ekrandan kaybolmadan hemen önce çağrılır. Kullanıcının yaptığı değişiklikleri kaydetmek için uygundur. |
| `viewDidDisappear()`   | View tamamen ekrandan kaybolduğunda çağrılır. Ağ isteklerini iptal etmek veya temizlik işlemleri için uygundur. |
| `deinit`               | ViewController bellekten tamamen kaldırıldığında çağrılır. Hafıza sızıntılarını önlemek için burada temizleme yapılabilir. |

## ViewController Yaşam Döngüsü Metotları
### Temel Yaşam Döngüsü Metotları
````swift
class MyViewController: UIViewController {
    
    override func loadView() {
        super.loadView()
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
````

## Hangi Aşamada Ne Yapılmalı?

### `loadView()`
- Eğer `UIViewController` içinde custom bir `UIView` oluşturmak istiyorsan, burada özelleştirme yapabilirsin.
- Genellikle varsayılan haliyle bırakılır.

### `viewDidLoad()`
- UI bileşenlerini oluştur ve konumlandır.
- API veya veritabanından verileri çek.
- İlk yükleme sırasında tek seferlik ayarlamaları yap.

### `viewWillAppear()`
- Kullanıcı her ekrana geldiğinde güncellenmesi gereken verileri burada yenile.
- Navigation Bar veya UI değişikliklerini yap.

### `viewDidAppear()`
- Animasyonları başlat.
- Ağ istekleri veya timer’ları çalıştır.

### `viewWillDisappear()`
- Kullanıcının yaptığı değişiklikleri kaydet.
- Açık kalmış animasyonları veya geçici UI öğelerini durdur.

### `viewDidDisappear()`
- Hafızada gereksiz nesneleri temizle.
- Arka planda çalışan görevleri iptal et.
- UI için fazla bellek kullanan nesneleri serbest bırak.

### `deinit`
- ViewController bellekten tamamen kaldırılırken çalışır.
- Observer’ları, NotificationCenter ve ağ bağlantılarını serbest bırak.

## Yaşam Döngüsünün Çalışma Sırası
```text
Bir ViewController ekrana yüklendiğinde şu sıra izlenir:
1️⃣ loadView()
2️⃣ viewDidLoad()
3️⃣ viewWillAppear()
4️⃣ viewDidAppear()
ViewController kapatıldığında veya başka bir sayfaya geçildiğinde ise:
5️⃣ viewWillDisappear()
6️⃣ viewDidDisappear()
7️⃣ Eğer ViewController tamamen yok oluyorsa → deinit()
```
---

# 5 - Extension Nedir? Neden, Nasıl ve Ne Zaman Kullanılır?

### Genel Tanım
**Extension**, Swift’te mevcut bir sınıf (class), yapı (struct), numaralandırma (enum) veya protokole (protocol) **yeni özellikler ve işlevler eklemeye** olanak tanır.  
Ancak, bir extension var olan türün **orijinal kaynak kodunu değiştirmeden** yeni yetenekler ekler, yani **override işlemi yapılamaz**.

### Extension Kullanım Amaçları

```text
✅ Mevcut bir sınıfa veya yapıya yeni metotlar ve özellikler eklemek.
✅ UIKit gibi Apple’ın framework’lerindeki sınıfları genişletmek.
✅ Kod tekrarını azaltarak daha temiz ve modüler bir yapı oluşturmak.
✅ Protokollere varsayılan implementasyon eklemek.
```
## Extension Nasıl Kullanılır?
### Temel Extension Kullanımı
````swift
extension String {
    func reversedString() -> String {
        return String(self.reversed())
    }
}

let text = "Swift"
print(text.reversedString()) // "tfiwS"
````
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
````swift
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
````
Bu örnekte Person yapısı Greetable protokolünü uyguladı, ancak greet() metodunu tanımlamadı.
Protokol extension sayesinde, varsayılan bir implementasyon sunmuş olduk.

**Önemli Not**: Bir protokolü implemente eden her yapı, protokolün sahip olduğu tüm metodları yazmak zorundadır. Eğer bu protokole bir extension yazarsanız extension içindeki metodların yazılma zorunluluğu yoktur. Örnek olarak bir **CollectionView** tanımladığınızda ve **UICollectionViewDataSource** protokolünü implement ettiğinizde **numberOfItemsInSection** ve **cellForItemAt** fonksiyonlarını eklemek zorundasınız. Fakat ihtiyacınız yoksa extension içinde tanımlanmış olan **numberOfSections** fonksiyonunu eklemek zorunda değilsiniz.

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

- **Non-Escaping Closure:** Closure, çağrıldığı fonksiyon içinde çalıştırılır ve fonksiyon tamamlandığında bellekten temizlenir. Varsayılan olarak tüm closure’lar **non-escaping** olarak kabul edilir.  
- **Escaping Closure:** Closure, fonksiyon tamamlandıktan sonra da çalışabilir. Bir değişkene atanarak saklanabilir veya asenkron bir işlemde kullanılabilir. **@escaping** ile belirtilmesi gerekir.  

Swift’in varsayılan yaklaşımı **non-escaping** olduğu için, sadece gerektiğinde **@escaping** kullanılır.  

### Escaping ve Non-Escaping Farkları  

| Özellik                 | Non-Escaping Closure | Escaping Closure |
|-------------------------|---------------------|-----------------|
| **Fonksiyon bitince çalışır mı?** | Hayır | Evet |
| **Bellekte ne kadar tutulur?** | Geçici (stack bellekte saklanır) | Daha uzun süre tutulabilir (heap bellekte saklanır) |
| **Performans etkisi** | Daha hızlı ve güvenli | Dikkatli yönetilmezse memory leak olabilir |
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

Çıktı: 
(2 saniye sonra)
Veri yüklendi  
```
Burada **closure, `DispatchQueue` içinde asenkron çalıştığı için `@escaping` olarak tanımlanmalıdır.**  

Eğer **@escaping** kullanılmazsa, closure fonksiyon bittikten sonra bellekten silinir ve çağrılmaya çalışıldığında hata alınır.  

**Escaping closure’lar daha esnektir ancak bellek yönetimi açısından dikkatli kullanılmalıdır.**  


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
- Elemanlara **index numarası** ile erişilir.
- Tekrarlayan değerleri saklayabilir.
- **Dizi içindeki elemanların sırası korunur.**

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
- Array’e göre **arama işlemleri daha hızlıdır**.

**Kullanım:**
```swift
var uniqueNumbers: Set<Int> = [1, 2, 3, 4, 1, 2]
print(uniqueNumbers) // [1, 2, 3, 4] (sıra rastgele olabilir)
```
### Dictionary
**Tanım:** Her elemanın **bir anahtara (key) ve değere (value)** sahip olduğu bir koleksiyon türüdür.

**Özellikler:**
- Anahtarlar **benzersiz** olmalıdır.
- Değerler aynı anahtar altında saklanır.
- **Hızlı veri erişimi sağlar.**

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
| **Arama Hızı** | Yavaş | Daha hızlı | Çok hızlı |

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
  - **Büyük miktarda veriyi hızlıca getirmek gerektiğinde.**
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
### **forEach(_:)**
Her eleman için belirli bir işlemi gerçekleştirir (ancak yeni bir koleksiyon döndürmez).
```swift
let names = ["Ali", "Ayşe", "Veli"]
names.forEach { print($0) }
```
### **sorted(by:)**
Koleksiyon elemanlarını belirtilen koşula göre sıralar.
```swift
let numbers = [5, 2, 8, 3]
let sortedNumbers = numbers.sorted(by: <)
print(sortedNumbers) // [2, 3, 5, 8]
```
## Hangi Durumda Kullanılmalı?
- **map:** Bir koleksiyonun her elemanına işlem uygulayıp yeni bir koleksiyon oluşturmak için.
- **filter:** Belirli bir koşulu sağlayan elemanları almak için.
- **reduce:** Bir koleksiyonun tüm elemanlarını tek bir değere indirgemek için.
- **forEach:** Koleksiyon elemanları üzerinde yan etki oluşturarak döngü çalıştırmak için.
- **sorted:** Elemanları belirli bir sıralama ile düzenlemek için.
---

# 9 - ARC (Automatic Reference Counting) Nedir? Ne İşe Yarar?

### Genel Tanım
ARC (Automatic Reference Counting), Swift dilinde bellek yönetimini otomatik olarak gerçekleştiren bir sistemdir. ARC, nesnelerin (instance) bellekten ne zaman kaldırılacağını belirlemek için **referans sayacı** kullanır. **Gereksiz bellek kullanımı ve bellek sızıntılarını önlemek için tasarlanmıştır.**

Swift, geliştiricinin manuel olarak bellek yönetimi yapmasını gerektirmeden ARC sayesinde **otomatik olarak** bellek tahsisini ve serbest bırakılmasını gerçekleştirir.

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
Güçlü döngüler (retain cycle) oluşmasını önlemek için `weak` kullanılır. ARC, weak referansları **hesaplamaz** ve referans sayacı **arttırılmaz**.
```swift
class Employee {
    var name: String
    weak var department: Department?
    init(name: String) {
        self.name = name
    }
}

class Department {
    var title: String
    var manager: Employee?
    init(title: String) {
        self.title = title
    }
}

var employee: Employee? = Employee(name: "Mehmet")
var department: Department? = Department(title: "IT")

employee?.department = department // Weak kullanıldığı için retain cycle oluşmaz
```

### **Unowned Reference (Sahipsiz Referans)**
Weak referansa benzer ancak ARC, `unowned` referansları sıfırlandığında bile takip etmeye devam eder. Bu yüzden unowned kullanılan nesneler bellekten kaldırılmadan önce erişilmeye çalışılırsa uygulama çöker.
```swift
class User {
    var name: String
    unowned var card: CreditCard
    init(name: String, card: CreditCard) {
        self.name = name
        self.card = card
    }
}

class CreditCard {
    var number: String
    var owner: User
    init(number: String, owner: User) {
        self.number = number
        self.owner = owner
    }
}
```
Bu örnekte **unowned** kullanıldığı için, `User` nesnesi silindiğinde hala referans tutan `CreditCard` nesnesi çökmeye neden olabilir.

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
    weak var pet: Pet? // Weak referans kullanıldı
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
```
Bu çözümde `weak` referans kullanıldığı için retain cycle oluşmaz.

## Sonuç

- **ARC**, Swift’te bellek yönetimini otomatik hale getirerek manuel yönetim ihtiyacını ortadan kaldırır.
- **Referans sayacı** ile nesnelerin ne zaman bellekten silineceğini belirler.
- **Retain cycle** oluşmasını önlemek için **weak ve unowned referanslar** kullanılmalıdır.
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
Eğer `person.pet` nil ise kod çökmeyecek, **nil yerine varsayılan bir değer dönecektir.**

## Hangi Durumda Hangisi Kullanılmalı?
| Yöntem | Kullanım Amacı |
|--------|---------------|
| **Forced Unwrapping (!)** | Değişkenin kesinlikle nil olmayacağından eminsek. |
| **Optional Binding (if let, guard let)** | Değişkenin nil olup olmadığını güvenli şekilde kontrol etmek için. |
| **Nil-Coalescing Operator (??)** | Nil olursa varsayılan bir değer kullanmak için. |
| **Optional Chaining (?.)** | İç içe geçmiş optional değerleri güvenli şekilde çağırmak için. |

## Sonuç
- **Optional**, nil hatalarını önleyerek daha güvenli kod yazmayı sağlar.
- **Forced unwrapping (`!`) dikkatli kullanılmalıdır, aksi halde uygulama çökebilir.**
- **Güvenli unwrap yöntemleri (if let, guard let, ??) tercih edilmelidir.**
- **Optional chaining, nested değerlerde güvenli erişim sağlar.**
---

# 11 - Access Level (Erişim Seviyeleri) Nedir?

### Genel Tanım
Swift'te **Access Level (Erişim Seviyeleri)**, bir kodun **hangi bileşenlere erişebileceğini** belirleyen mekanizmadır. Bu sistem, kodun daha modüler ve güvenli olmasını sağlar. Swift’te **beş farklı erişim seviyesi** vardır:

1. **open** – En geniş erişim seviyesi, modüller arası kullanım mümkündür.
2. **public** – Açık ancak yalnızca okuma amaçlı kullanılabilir, miras alınabilir.
3. **internal** – Varsayılan erişim seviyesi, aynı modül içinde erişime izin verir.
4. **fileprivate** – Aynı dosya içinde erişime izin verir.
5. **private** – Yalnızca aynı sınıf veya kapsam içinde kullanılabilir.

## Access Level Türleri
### 1. **Open ve Public**
- `open` ve `public`, en geniş erişim seviyeleridir ve **modüller (framework’ler) arasında kullanılabilirler**.
- `open`, miras almayı (subclassing) ve override etmeyi desteklerken, `public` **override etmeyi kısıtlar**.

```swift
open class Animal {
    public var name: String
    init(name: String) {
        self.name = name
    }
    open func makeSound() {
        print("Animal sound")
    }
}
```
**Ne zaman kullanılır?**
- `open`: Kütüphanelerde ve başkalarının genişletmesini istediğimiz sınıflarda.
- `public`: Kütüphanelerde ancak **miras alınmasını istemediğimiz** sınıflarda.

### 2. **Internal (Varsayılan)**
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

### 3. **Fileprivate**
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

### 4. **Private**
- **Yalnızca tanımlandığı kapsam içinde** erişilebilir.
- **Sınıf içindeki diğer metodlar ve değişkenler tarafından görülemez.**

```swift
class Person {
    private var id: String = "12345"
    
    private func generateID() {
        self.id = UUID().uuidString
    }
}
```
**Ne zaman kullanılır?**
- **Gerçekten gizli olması gereken veriler için.**
- **Başka hiçbir bileşenin erişmesini istemediğimiz özellikler için.**

## Karşılaştırma Tablosu

| Access Level   | Aynı Sınıf | Aynı Dosya | Aynı Modül | Başka Modüller |
|---------------|-----------|------------|------------|--------------|
| **open**      | ✅        | ✅         | ✅         | ✅ (Miras alabilir) |
| **public**    | ✅        | ✅         | ✅         | ✅ (Miras alınamaz) |
| **internal**  | ✅        | ✅         | ✅         | ❌ |
| **fileprivate** | ✅       | ✅         | ❌         | ❌ |
| **private**   | ✅        | ❌         | ❌         | ❌ |

## Hangi Durumda Hangisi Kullanılmalı?

- **open/public**: Başka modüllerden erişime açık olması gereken sınıflar için.
- **internal**: Varsayılan olarak kullanılır, modül içindeki genel kullanım için uygundur.
- **fileprivate**: Aynı dosya içindeki kod arasında veri paylaşımı için uygundur.
- **private**: Yalnızca aynı kapsam içinde erişilmesi gereken veriler için kullanılır.
---

# 12 - SOLID Prensipleri

### Genel Tanım
SOLID, **nesne yönelimli programlama (OOP)** için beş temel prensibi ifade eden bir kısaltmadır. Bu prensipler, kodun **daha esnek, sürdürülebilir ve yönetilebilir** olmasını sağlar.

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
**Bir sınıf genişletilebilir olmalı ancak değiştirilmemelidir.**

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
**Üst seviyedeki sınıflar, alt seviyedeki sınıflara bağımlı olmamalıdır.**

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
Bu sayede **farklı bir veritabanı kullanmak istediğimizde, `DataManager` sınıfını değiştirmemize gerek kalmaz**.
### Sonuç
SOLID prensipleri, daha sürdürülebilir, genişletilebilir ve hatasız bir yazılım geliştirmeyi sağlar. Bu kuralları takip ederek kodunuzu daha okunabilir, yönetilebilir ve esnek hale getirebilirsiniz.

---


## Şirketler
Şirketlerin işe alım süreci ile ilgili detaylar.

### Trendyol

### Appcent
