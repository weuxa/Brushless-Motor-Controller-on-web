# Yeniçeriler Dual Motor Controller

Tek GitHub deposunda iki ayrı BLE web kumandası ve iki bağımsız Deneyap firmware'i.

## Klasör yapısı

```text
index.html                         # Sistem seçim sayfası
assets/style.css                   # Ortak tasarım
submarine/index.html               # 4 iticili denizaltı kumandası
single-motor/index.html            # Tek motor kumandası
firmware/four-thruster/four_thruster.ino
firmware/single-motor/single_motor.ino
```

## GitHub Pages yayınlama

1. Bu klasörün içeriğini GitHub'da yeni bir repository'nin kök dizinine yükle.
2. Repository > **Settings > Pages** bölümünde **Deploy from a branch** seç.
3. Branch olarak `main`, klasör olarak `/ (root)` seçip kaydet.
4. Yayınlandıktan sonra:
   - Ana seçim sayfası: `https://KULLANICI.github.io/REPO/`
   - 4 iticili denizaltı: `https://KULLANICI.github.io/REPO/submarine/`
   - Tek motor: `https://KULLANICI.github.io/REPO/single-motor/`

Web Bluetooth için destekleyen bir tarayıcıda HTTPS kullan. Masaüstünde Chrome/Edge; Android'de Chrome önerilir. iOS tarayıcı desteği sınırlı olabilir.

## İki farklı BLE cihaz adı

| Sistem | Sayfadaki cihaz filtresi | Arduino'daki `DEVICE_NAME` |
|---|---|---|
| 4 iticili denizaltı | `Yeniceri-4T` | `Yeniceri-4T` |
| Tek motor | `Brushless-Motor-1T` | `Brushless-Motor-1T` |

Her sistem için yalnızca kendi `.ino` dosyasını kendi kartına yükle. İki sistemin cihaz adları farklı olduğu için sayfa yanlış kartı seçmez. Her iki sistem de orijinal projenin BLE UUID'lerini ve `S`, `H`, `M,...` komut yapısını kullanır.

## 4 iticili sistemin komutları

`M,left,right,vertical` değerleri -100 ile +100 arasındadır. Arka sol ve arka sağ iticiler farklı değer alabilir; iki ön itici aynı dikey komutu alır. Web arayüzü yatay ve dikey girdileri aynı komutta birleştirir.

**Önemli ESC şartı:** İleri/geri, yerinde dönme ve yükselme/alçalmanın iki yönlü itiş verebilmesi için bu firmware `BIDIRECTIONAL_ESC true` ayarıyla yazılmıştır. Bu yalnızca ESC'nin çift yönlü/geri vites modunu gerçekten desteklemesi ve 1500 µs nötr için doğru kalibre edilmiş olması halinde güvenlidir. Tek yönlü uçak ESC'sinde 1500 µs gaz komutu olabilir. ESC modelini doğrulamadan bataryayı bağlama. ESC'lerin yön ve nötr kalibrasyonunu üretici talimatına göre yap.

## Tek motor sistemi

Tek motor firmware'i varsayılan olarak tek yönlü ESC kullanır: `ESC_BIDIRECTIONAL false`, 1000 µs durma ve 1000–2000 µs ileri gaz aralığı. ESC'nin çift yönlü olduğu doğrulanırsa bu ayar `true` yapılabilir; o durumda durma/nötr 1500 µs, güç aralığı 1520–2000 µs olur. ESC modelini doğrulamadan ayarı değiştirme. Web'deki çalıştır düğmesi basılı tutulduğu sürece güç gönderir; bırakıldığında `S` yollar.

## Pinler ve güvenlik

- 4 iticili: `D0`, `D1`, `D2`, `D3`
- Tek motor: `D0`
- Deneyap Kartı modelinin pin etiketlerini kontrol et; farklı board variant'ında `D0` tanımları veya fiziksel GPIO eşleşmeleri değişebilir.
- ESC sinyal GND'si ile Deneyap GND ortak olmalı. ESC motor faz bağlantıları üç kablonun tamamıyla ve sağlam lehim/konnektörle yapılmalı.
- Pervaneler sökülüyken test et. BLDC motorlar, ESC ve pervaneler yüksek akım ve ciddi mekanik risk oluşturabilir.
- `S` durdurma komutu, BLE bağlantı kesilmesi ve 1200 ms komut/heartbeat zaman aşımı motorları durma sinyaline alır. Yazılım acil durdurma sisteminin fiziksel bir yedeği değildir.
- Sayfa yalnızca kontrol komutlarını gösterir. Basınç, derinlik, pusula, batarya gerilimi, sıcaklık veya gerçek RPM sensörleri eklenmediğinden bunlara ait sahte telemetri gösterilmez.

## Geliştirme temeli

BLE servis UUID'si, characteristic UUID'si ve komut protokolü, kullanıcının paylaştığı `weuxa/Brushless-Motor-Controller-on-web` projesiyle uyumlu tutuldu; arayüzler ve firmware'ler bu iki sistem için ayrı olarak düzenlendi.
