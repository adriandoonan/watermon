#include <WiFi.h>
#include <WiFiClientSecure.h>
#include <HTTPClient.h>

const int sensorPin = 5; // GPIO5

const char* WIFI_SSID = "YOUR_WIFI_NAME";
const char* WIFI_PASS = "YOUR_WIFI_PASS";

const char* TELEGRAM_BOT_TOKEN = "YOUR_TELEGRAM_BOT_TOKEN";
const char* TELEGRAM_CHAT_ID   = "YOUR_TELEGRAM_CHAT_ID"; // group chats will start with a minus sign

/* ================= TIMING CONSTANTS ================= */
const unsigned long SAMPLE_EVERY_MS = 1000UL;        // read sensor every 1s
const unsigned long DEBOUNCE_MS     = 2000UL;        // must be stable 2s
const unsigned long LOW_REPEAT_MS   = 21600000UL;    // 6 hours

/* ================= STATE VARIABLES ================= */
unsigned long lastSampleAt      = 0;
unsigned long lastRawChangeAt   = 0;
unsigned long lastLowNotifyAt   = 0;

int  lastRaw = -1;
bool isLow   = false;

/* ================= HELPERS ================= */
static String urlEncode(const String &s) {
  String out;
  char buf[4];
  for (size_t i = 0; i < s.length(); i++) {
    char c = s[i];
    if (isalnum((unsigned char)c) || c=='-' || c=='_' || c=='.' || c=='~') {
      out += c;
    } else if (c == ' ') {
      out += "%20";
    } else {
      snprintf(buf, sizeof(buf), "%%%02X", (unsigned char)c);
      out += buf;
    }
  }
  return out;
}

bool telegramSend(const String &text) {
  WiFiClientSecure client;
  client.setInsecure();

  HTTPClient https;
  String url = "https://api.telegram.org/bot" + String(TELEGRAM_BOT_TOKEN) + "/sendMessage";
  String body = "chat_id=" + String(TELEGRAM_CHAT_ID) + "&text=" + urlEncode(text);

  if (!https.begin(client, url)) return false;
  https.addHeader("Content-Type", "application/x-www-form-urlencoded");

  int httpCode = https.POST(body);
  https.end();

  Serial.printf("Telegram HTTP %d\n", httpCode);
  return (httpCode == 200);
}

/* ================= SETUP ================= */
void setup() {
  Serial.begin(115200);
  delay(200);

  pinMode(sensorPin, INPUT);  // or INPUT_PULLUP if you add pull-up

  WiFi.begin(WIFI_SSID, WIFI_PASS);
  Serial.print("WiFi connecting");
  while (WiFi.status() != WL_CONNECTED) {
    delay(300);
    Serial.print(".");
  }
  Serial.println("\nWiFi connected");

  telegramSend("ESP32-C3 booted ✅");
}

/* ================= LOOP ================= */
void loop() {
  unsigned long now = millis();

  // Sample rate limiter
  if (now - lastSampleAt < SAMPLE_EVERY_MS) return;
  lastSampleAt = now;

  int raw = digitalRead(sensorPin);

  // Track raw changes for debounce
  if (raw != lastRaw) {
    lastRaw = raw;
    lastRawChangeAt = now;
  }

  // Define logic: LOW = water missing
  bool lowCandidate = (raw == LOW);

  // Debounced state change
  if ((now - lastRawChangeAt) >= DEBOUNCE_MS) {
    if (isLow != lowCandidate) {
      isLow = lowCandidate;

      if (isLow) {
        Serial.println("⚠️ Water LOW");
        telegramSend("⚠️ Water is low");
        lastLowNotifyAt = now;
      } else {
        Serial.println("✅ Water OK");
        telegramSend("✅ Water back OK");
      }
    }
  }

  // Reminder while still low
  if (isLow && (now - lastLowNotifyAt) >= LOW_REPEAT_MS) {
    telegramSend("⚠️ Water still low (reminder)");
    lastLowNotifyAt = now;
  }
}
