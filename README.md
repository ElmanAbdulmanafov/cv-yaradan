# CV Yaradan

Peşəkar CV hazırlayan tətbiq: 8 şablon, rəng seçimi, canlı önizləmə, PDF yükləmə.
Veb-saytı kimi də, Android proqramı kimi də işləyir.

## Qovluq quruluşu

| Yol | Nə üçündür |
|---|---|
| `www/` | Tətbiqin özü (HTML/CSS/JS). Brauzerdə birbaşa `www/index.html` açmaq olar. |
| `www/lib/html2pdf.bundle.min.js` | PDF yaradan kitabxana (offline işləyir). |
| `android/` | Capacitor ilə yaradılmış Android Studio layihəsi. |
| `assets/` | Tətbiq ikonu və splash mənbə şəkilləri. |
| `capacitor.config.json` | Tətbiq adı və ID: `az.cvyaradan.app`. |

## Android APK yığmaq

Tələblər: Android Studio (SDK 36) və Node.js.

```bash
npm install
npx cap sync android
cd android
./gradlew assembleDebug
```

Hazır fayl: `android/app/build/outputs/apk/debug/app-debug.apk`.
Telefona qurmaq üçün USB ilə qoşub:

```bash
adb install -r android/app/build/outputs/apk/debug/app-debug.apk
```

Və ya layihəni Android Studio-da açın: `File → Open → cv-generator/android`, sonra `Run`.

### Avast / antivirus qeydi

Bu kompüterdə Avast HTTPS trafikini yoxlayır və Gradle-ın yükləmələri sertifikat xətası verirdi.
Həll `android/certs/cacerts` faylındadır (JDK sertifikatları + Avast kök sertifikatı) və
`android/gradle.properties` ona yönəlib. Başqa kompüterdə bu sətirləri silmək olar.

Hazır APK: `CV-Yaradan-debug.apk` (layihə kökündə, hər yığımdan sonra əl ilə kopyalanır).

## Veb-saytdan dəyişiklik etdikdən sonra

`www/` içində nəsə dəyişdikdə Android layihəsinə kopyalamaq üçün:

```bash
npx cap sync android
```

## Play Store-a yükləmə

### 1. İmzalama açarı (artıq yaradılıb)

- Açar: `android/keystore/cv-yaradan-upload.jks` (alias `upload`)
- Parol: `android/keystore.properties` faylında
- Hər ikisi `.gitignore`-dadır, git-ə düşmür.

**MÜTLƏQ** bu iki faylın ehtiyat nüsxəsini təhlükəsiz yerə (parol meneceri, şifrəli disk) kopyalayın.
Açar itərsə tətbiqi yeniləmək mümkün olmayacaq (Play App Signing açıq olsa Google dəstəyi ilə bərpa etmək olur, amma vaxt aparır).

### 2. Release yığımı

```bash
npx cap sync android
cd android
./gradlew bundleRelease
```

Nəticə: `android/app/build/outputs/bundle/release/app-release.aab` — Play Console-a məhz bu fayl yüklənir.
Yan nüsxə layihə kökündə: `CV-Yaradan-release.aab`. Test üçün imzalı APK: `CV-Yaradan-release.apk`.

Hər yeni yükləmədə `android/app/build.gradle` içində `versionCode`-u 1 artırın (1 → 2 → 3…) və `versionName`-i yeniləyin.

### 3. Play Console addımları

1. [play.google.com/console](https://play.google.com/console) — developer hesabı (birdəfəlik 25 USD).
2. **Create app** → ad "CV Yaradan", App, Free, dil Azerbaijani.
3. **Set up your app** siyahısındakı hər bəndi doldurun; cavablar `store/listing.md` faylındadır
   (privacy policy, ads, content rating, target audience, data safety, store listing).
4. **Store listing**: mətnlər və qrafiklər `store/` qovluğundadır.
5. **Testing → Internal testing** → Create release → `app-release.aab` yükləyin → Review → Rollout.
   Play App Signing təklif olunanda **qəbul edin** (Google yayım açarını özü saxlayır, bizimki "upload key" olur).
6. Yeni şəxsi hesablar üçün Google tələb edir: Production-a çıxmazdan əvvəl **Closed testing** mərhələsində
   ən azı 12 testçi 14 gün ərzində tətbiqi quraşdırmalıdır. Testçiləri Closed testing → Testers bölməsində e-poçtla əlavə edin.
7. Sonra **Production → Create release** → eyni AAB (və ya yeni versiya) → Submit for review. Yoxlama adətən 1–7 gün çəkir.

### 4. Məxfilik siyasəti

`www/privacy.html` faylını internetdə yerləşdirin. Ən asan yol GitHub Pages:
repo yaradın, `www` qovluğunu yükləyin, Settings → Pages → Branch main / folder `/www`.
Link belə olacaq: `https://<istifadəçi>.github.io/<repo>/privacy.html`.
