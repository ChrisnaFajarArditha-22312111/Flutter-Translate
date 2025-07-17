## 3. Integrasi dengan Aplikasi Flutter Frontend

Aplikasi Flutter akan menjadi antarmuka pengguna tempat pengguna berinteraksi dengan sistem. Ini akan mengirimkan teks ke Flask API dan menampilkan hasil terjemahan.

### 3.1. Struktur Proyek Flutter

Asumsikan saya memiliki struktur proyek Flutter seperti ini:

```
translator_app/
├── lib/
│   ├── main.dart
│   ├── models/
│   │   └── translation_response.dart # Model data untuk respons API
│   ├── services/
│   │   └── translation_service.dart # Layanan untuk memanggil API
│   └── screens/
│       └── home_screen.dart # Layar utama aplikasi
├── pubspec.yaml
└── ...
```

### 3.2. File `pubspec.yaml`

Tambahkan dependensi `http` untuk melakukan panggilan HTTP ke Flask API.

```yaml
name: translator_app
description: Aplikasi terjemahan Indonesia ke Inggris

publish_to: \'none\'

version: 1.0.0+1

environment:
  sdk: \'>=3.0.0 <4.0.0\'

dependencies:
  flutter:
    sdk: flutter
  http: ^1.1.0 # Tambahkan ini
  cupertino_icons: ^1.0.2

dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^3.0.0

flutter:
  uses-material-design: true
```

Setelah menambahkan dependensi, jalankan `flutter pub get` di terminal proyek Flutter saya.

### 3.3. File `lib/models/translation_response.dart`

Definisikan model data untuk mengurai respons JSON dari Flask API.

```dart
class TranslationResponse {
  final bool success;
  final String originalText;
  final String translatedText;
  final String sourceLanguage;
  final String targetLanguage;
  final String? error;

  TranslationResponse({
    required this.success,
    required this.originalText,
    required this.translatedText,
    required this.sourceLanguage,
    required this.targetLanguage,
    this.error,
  });

  factory TranslationResponse.fromJson(Map<String, dynamic> json) {
    return TranslationResponse(
      success: json[\'success\'] ?? false,
      originalText: json[\'original_text\'] ?? \'\',
      translatedText: json[\'translated_text\'] ?? \'\',
      sourceLanguage: json[\'source_language\'] ?? \'\',
      targetLanguage: json[\'target_language\'] ?? \'\',
      error: json[\'error\'],
    );
  }
}
```

### 3.4. File `lib/services/translation_service.dart`

Buat layanan untuk melakukan panggilan HTTP ke Flask API. **Penting: Ganti `http://your-flask-api-url.com` dengan URL Flask API saya yang sebenarnya.**

```dart
import \'dart:convert\';
import \'package:http/http.dart\' as http;
import \'../models/translation_response.dart\';

class TranslationService {
  // Ganti dengan URL Flask API saya
  static const String baseUrl = \'http://your-flask-api-url.com\';
  
  static Future<TranslationResponse> translateText(String text) async {
    try {
      final response = await http.post(
        Uri.parse(\'$baseUrl/api/translate\'),
        headers: {
          \'Content-Type\': \'application/json\',
        },
        body: jsonEncode({
          \'text\': text,
        }),
      );

      if (response.statusCode == 200) {
        final Map<String, dynamic> data = jsonDecode(response.body);
        return TranslationResponse.fromJson(data);
      } else {
        return TranslationResponse(
          success: false,
          originalText: text,
          translatedText: \'\',
          sourceLanguage: \'id\',
          targetLanguage: \'en\',
          error: \'HTTP Error: ${response.statusCode}\,\',
        );
      }
    } catch (e) {
      return TranslationResponse(
        success: false,
        originalText: text,
        translatedText: \'\',
        sourceLanguage: \'id\',
        targetLanguage: \'en\',
        error: \'Network Error: $e\',
      );
    }
  }
}
```

### 3.5. File `lib/screens/home_screen.dart`

Ini adalah UI utama aplikasi, tempat pengguna memasukkan teks dan melihat hasil terjemahan. Ini akan menggunakan `TranslationService` untuk berkomunikasi dengan backend.

```dart
import \'package:flutter/material.dart\';
import \'../services/translation_service.dart\';
import \'../models/translation_response.dart\';

class HomeScreen extends StatefulWidget {
  const HomeScreen({super.key});

  @override
  State<HomeScreen> createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  final TextEditingController _textController = TextEditingController();
  String _translatedText = \'\';
  bool _isLoading = false;
  String? _errorMessage;

  Future<void> _translateText() async {
    if (_textController.text.trim().isEmpty) {
      setState(() {
        _errorMessage = \'Silakan masukkan teks yang akan diterjemahkan\';
      });
      return;
    }

    setState(() {
      _isLoading = true;
      _errorMessage = null;
    });

    try {
      final response = await TranslationService.translateText(_textController.text);
      
      setState(() {
        if (response.success) {
          _translatedText = response.translatedText;
        } else {
          _errorMessage = response.error ?? \'Terjadi kesalahan saat menerjemahkan\';
        }
        _isLoading = false;
      });
    } catch (e) {
      setState(() {
        _errorMessage = \'Terjadi kesalahan: $e\';
        _isLoading = false;
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text(\'Translator ID-EN\'),
        backgroundColor: Colors.blue,
        foregroundColor: Colors.white,
      ),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.stretch,
          children: [
            const Text(
              \'Teks Bahasa Indonesia:\',
              style: TextStyle(
                fontSize: 16,
                fontWeight: FontWeight.bold,
              ),
            ),
            const SizedBox(height: 8),
            TextField(
              controller: _textController,
              maxLines: 4,
              decoration: const InputDecoration(
                border: OutlineInputBorder(),
                hintText: \'Masukkan teks yang akan diterjemahkan...\',
              ),
            ),
            const SizedBox(height: 16),
            ElevatedButton(
              onPressed: _isLoading ? null : _translateText,
              style: ElevatedButton.styleFrom(
                backgroundColor: Colors.blue,
                foregroundColor: Colors.white,
                padding: const EdgeInsets.symmetric(vertical: 12),
              ),
              child: _isLoading
                  ? const Row(
                      mainAxisAlignment: MainAxisAlignment.center,
                      children: [
                        SizedBox(
                          width: 20,
                          height: 20,
                          child: CircularProgressIndicator(
                            strokeWidth: 2,
                            valueColor: AlwaysStoppedAnimation<Color>(Colors.white),
                          ),
                        ),
                        SizedBox(width: 8),
                        Text(\'Menerjemahkan...\'),
                      ],
                    )
                  : const Text(
                      \'Terjemahkan\',
                      style: TextStyle(fontSize: 16),
                    ),
            ),
            const SizedBox(height: 24),
            const Text(
              \'Hasil Terjemahan (Bahasa Inggris):\',
              style: TextStyle(
                fontSize: 16,
                fontWeight: FontWeight.bold,
              ),
            ),
            const SizedBox(height: 8),
            Container(
              width: double.infinity,
              constraints: BoxConstraints(minHeight: 100), // Menggunakan constraints untuk min-height
              padding: const EdgeInsets.all(12),
              decoration: BoxDecoration(
                border: Border.all(color: Colors.grey),
                borderRadius: BorderRadius.circular(4),
                color: Colors.grey[50],
              ),
              child: _errorMessage != null
                  ? Text(
                      _errorMessage!,
                      style: const TextStyle(
                        color: Colors.red,
                        fontSize: 14,
                      ),
                    )
                  : Text(
                      _translatedText.isEmpty ? \'Hasil terjemahan akan muncul di sini...\' : _translatedText,
                      style: TextStyle(
                        fontSize: 14,
                        color: _translatedText.isEmpty ? Colors.grey : Colors.black,
                      ),
                    ),
            ),
          ],
        ),
      ),
    );
  }

  @override
  void dispose() {
    _textController.dispose();
    super.dispose();
  }
}
```

### 3.6. File `lib/main.dart`

File utama aplikasi yang menjalankan `HomeScreen`.

```dart
import \'package:flutter/material.dart\';
import \'screens/home_screen.dart\';

void main() {
  runApp(const TranslatorApp());
}

class TranslatorApp extends StatelessWidget {
  const TranslatorApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: \'Translator ID-EN\',
      theme: ThemeData(
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.blue),
        useMaterial3: true,
      ),
      home: const HomeScreen(),
      debugShowCheckedModeBanner: false,
    );
  }
}
```

### 3.7. Konfigurasi Network Permission

Pastikan saya telah menambahkan izin jaringan yang diperlukan untuk Android dan iOS agar aplikasi dapat berkomunikasi dengan API eksternal.

*   **Android (`android/app/src/main/AndroidManifest.xml`):**
    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    ```

*   **iOS (`ios/Runner/Info.plist`):**
    ```xml
    <key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads</key>
        <true/>
    </dict>
    ```

Ini adalah panduan komprehensif dengan kode untuk setiap bagian dari aplikasi terjemahan saya. Pastikan untuk menyesuaikan URL API di aplikasi Flutter dan memindahkan model NLP ke direktori yang benar di proyek Flask saya.




