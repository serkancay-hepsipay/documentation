# **Pay With HepsiPay iOS Documentation**

# Contents
- [Integration](#integration)
  - [Option 1 - Swift Package Manager - Package.swift](#spmpackage)
  - [Option 2 - Swift Package Manager - Project Package Dependencies](#spmproject)
  - [Option 3 - Manual](#manual)
- [Initialization](#initialization)
- [Usage](#usage)

# <a name="integration"> Integration </a>

### <a name="spmpackage">Option 1 - Swift Package Manager - Package.swift</a>
Projenizde `Package.swift` kullanıyorsanız Package dependencies kısmına aşağıdaki satırı ekleyin.
```swift
dependencies: [
    ...
    .package(url: "https://gitea.com/hepsipay/PayWithHPSPM.git", from: "1.0.0")
]
```
Kullanacağınız target için dependencies kısmına ekleme yapın.
```swift
.target(
    name: "YourTarget",
    dependencies: [
        "YourDependencies",
        ...
        "PayWithHPSPM"
    ]
)
```
### <a name="spmproject">Option 2 - Swift Package Manager - Project Package Dependencies</a>
`Package.swift` kullanmıyorsanız `Project -> Settings -> Package Dependencies` sekmesinde + butonuna basarak `Search or Enter Package URL` kısmına aşağıdaki URL'i girip ekleyin.
```
https://gitea.com/hepsipay/PayWithHPSPM.git
```
`Project -> Target Settings -> General -> Frameworks, Libraries, and Embedded Content` kısmında `PayWithHPSPM` kütüphanesi görünür olmalıdır. Ekli değilse + butonundan ekleyin.

### <a name="manual">Option 3 - Manual</a>
Repository içerisinde artifacts klasöründe bulunan PayWithHPFramework.xcframework dosyasını projenizin içine sürükleyip bırakın. Embed kısmında `Embed & Sign` seçin.

`Project -> Target Settings -> General -> Frameworks, Libraries, and Embedded Content` kısmında `PayWithHPFramework.xcframework` dosyası görünür olmalıdır, ekli değilse soldaki proje ağacından sürükleyip ekleyin.


# <a name="initialization">Initialization</a>

Framework import edilir.
```swift
import PayWithHPFramework
```

`PayWithHPManager.getPayWithHPView` metodu aşağıdaki gibi çağırılır. Bu metod, akışın sağlanacağı bir UIView döner.
```swift
let payWithHPView = PayWithHPManager.getPayWithHPView(
    model: .init(
        token: "MERCHANT_TOKEN",
        uniqueDeviceId: "UNIQUE_DEVICE_ID",
        paymentAvailableHandler: { isPaymentAvailable in
            self.payButton.isUserInteractionEnabled = isPaymentAvailable
            self.payButton.setBackgroundColor(isPaymentAvailable ? .accent : .gray)
        },
        paymentCompleteHandler: { isPaymentSuccess, paymentOption in
            if isPaymentSuccess {
                let successVC = PaymentFinishViewController()
                successVC.paymentOption = paymentOption
                self.navigationController?.pushViewController(successVC, animated: true)
            }
        }
    )
)
```

**Model Parametreleri**
- **`token: String`**: Hepsipay token
- **`uniqueDeviceId: String?`**: Merchant tarafından verilen unique device id. (Opsiyonel)
- **`paymentAvailableHandler`**: Ödeme yapabilme durumunun değiştiği durumlarda buraya düşer.
  - **`isPaymentAvailable: Bool`**: true ise ödeme yapabilir, false ise yapmamalıdır. Merchant tarafında bulunan **Ödeme Yap** butonu bu değere göre aktif/pasif edilir.
- **`paymentCompleteHandler`**: Ödeme tamamlandıktan sonra buraya düşer.
  -  **`isPaymentSuccess: Bool`**: Ödemenin başarılı/başarısız gerçekleştiği bilgisi
  -  **`paymentOption: OptionType`**: Ödemenin hangi seçenekle yapıldığı bilgisi. (Wallet, Card, Loan)


# <a name="usage">Usage</a>

PayWithHPManager.getPayWithHPView metodundan dönen UIView nesnesi, istenen yere eklenebilir. Merchant tarafında bulunan **Ödeme Yap** butonuna basıldığında aşağıdaki metod çağırılır.

```swift
PayWithHPManager.makePayment()
```