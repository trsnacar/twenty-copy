# Twenty ile Özel CRM Kurulum Planı (Türkçe)

Bu şablon, `twenty` açık kaynak kod tabanını kullanarak **B2B satış odaklı** bir CRM'i hızlıca ayağa kaldırmak için hazırlanmıştır.

## 1) Hedef CRM Tasarımı

Bu paket aşağıdaki varlıkları (objects) önerir:

- `Lead` (Potansiyel Müşteri)
- `Deal` (Fırsat)
- `Customer` (Müşteri Hesabı)
- `Task` (Görev)
- `Interaction` (Etkileşim)

> `Person` ve `Company` standart objeleri ile birlikte kullanılacak şekilde tasarlanmıştır.

## 2) Veri Modeli ve Alanlar

Detaylı alan listesi için `sales-crm-blueprint.json` dosyasını kullanın.

Öne çıkan alanlar:

- Lead puanlama (`leadScore`)
- Satış aşaması (`stage`)
- Beklenen kapanış tarihi (`expectedCloseDate`)
- Tahmini tutar (`amount`)
- Son etkileşim tarihi (`lastInteractionAt`)

## 3) Twenty içinde uygulama adımları

Aşağıdaki adımları Twenty arayüzünden yapın:

1. **Settings → Data Model** bölümüne girin.
2. `Lead`, `Deal`, `Interaction` için custom object oluşturun.
3. JSON dosyasındaki alan tiplerine göre custom field'ları ekleyin.
4. İlişkileri bağlayın:
   - Lead → Company (many-to-one)
   - Deal → Company (many-to-one)
   - Deal → Person (owner/contact)
   - Interaction → Deal (many-to-one)
5. Her obje için en az bir **Table View** oluşturun.
6. `Deal` objesinde bir **Kanban View** açın ve kolonları `stage` alanına göre gruplayın.

## 4) Önerilen görünüm ve filtreler

### Lead Pipeline
- Filtre: `status != converted`
- Sıralama: `leadScore DESC`
- Gösterilecek alanlar: `fullName`, `company`, `source`, `leadScore`, `owner`

### Deal Pipeline (Kanban)
- Grup: `stage`
- Sıralama: `amount DESC`
- Filtre: `stage not in (won, lost)`

### Haftalık Satış Takibi
- Filtre: `expectedCloseDate this month`
- Sıralama: `expectedCloseDate ASC`

## 5) Otomasyon (Workflow) önerileri

1. **Lead Scoring Güncelleme**
   - Trigger: Lead güncellendi
   - Condition: `email` veya `company` dolu
   - Action: `leadScore += 10`

2. **Deal gecikme uyarısı**
   - Trigger: Zamanlayıcı (günlük)
   - Condition: `expectedCloseDate < today` ve `stage not in (won,lost)`
   - Action: Owner'a görev ata (`Task` oluştur)

3. **Yeni müşteri onboarding**
   - Trigger: Deal stage = `won`
   - Action: `Interaction` kaydı aç + onboarding görevleri yarat

## 6) Rol ve yetki önerisi

- `Sales Admin`: tüm objelerde tam yetki
- `Sales Rep`: kendi lead/deal kayıtlarını görme+düzenleme
- `Manager`: tüm pipeline'ı görme, sadece yorum ve stage güncelleme

## 7) Hızlı başlangıç checklist

- [ ] Workspace adı, logo ve domain markaya göre güncellendi
- [ ] Obje/alanlar oluşturuldu
- [ ] Kanban ve table view'lar kaydedildi
- [ ] Workflow'lar aktifleştirildi
- [ ] Rol/yetkiler test edildi
- [ ] 10 örnek kayıt ile demo veri girildi

---

İsterseniz sonraki adımda bu blueprint'i sizin sektörünüze göre (ör. emlak, ajans, SaaS, distribütör) özelleştirebilirim.
