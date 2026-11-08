# Prime Force backend — Railway setup (Hindi)

## ZIP में क्या है
यह Node.js backend source है। इसमें कोई live API key, `.env` file या Firebase service-account credential शामिल नहीं है। Node.js 18 या नया संस्करण चाहिए।

## Deploy करने से पहले
- ZapUPI से नई API key लें; chat में भेजी गई सभी keys exposed हैं, उन्हें revoke/rotate करें। नई key केवल Railway secret variable में डालें।
- Prime Force में esports betting feature है। ZapUPI Terms gaming apps के लिए prior written approval माँगते हैं और wagering को prohibit करते हैं; लिखित approval मिलने तक live payments enable न करें।
- Firebase Realtime Database की Rules public न रखें। Rules को सुरक्षित और backend service account को न्यूनतम आवश्यक permission दें।

## Deploy का तरीका
1. ZIP extract करके इसकी files GitHub repository के root में upload/commit करें। इस ZIP में `package.json` और `server.js` root में हैं।
2. Railway में New Project → Deploy from GitHub repo चुनें और वही repository connect करें।
3. Railway service में Variables tab पर नीचे बताए गए required secrets/config जोड़ें। किसी key को chat, app code या APK में न डालें।
4. Service → Settings → Networking से public HTTPS domain generate करें।
5. `ZAPUPI_WEBHOOK_URL` को उस domain के साथ `/api/payments/zapupi/webhook` जोड़कर set करें। उदाहरण format: `https://YOUR-DOMAIN/api/payments/zapupi/webhook`.
6. Deploy पूरा होने पर `https://YOUR-DOMAIN/health` खोलें; उत्तर `{"ok":true}` होना चाहिए।
7. Prime Force Admin app → Settings में Backend server address को केवल domain के रूप में set करें: `https://YOUR-DOMAIN` (webhook path इसमें न जोड़ें), फिर settings save करें।

## Railway Variables
Required:
- `BOT_TOKEN` — Telegram BotFather token, अगर Telegram image/notification functions इस्तेमाल करने हैं।
- `CHANNEL_ID` — Telegram channel ID, उसी integration के लिए।
- `FIREBASE_CONFIG` — Firebase service-account JSON, Railway secret के रूप में।

ZapUPI के लिए, written approval मिलने और production readiness confirm होने के बाद:
- `ZAPUPI_API_KEY` — ZapUPI merchant key; केवल Railway Variables में रखें।
- `ZAPUPI_WEBHOOK_URL` — `https://YOUR-DOMAIN/api/payments/zapupi/webhook`.
- `ZAPUPI_ALLOW_TEST_PAYMENTS` — production में `false` या unset रखें।
- `ZAPUPI_MAX_AMOUNT_INR` — optional per-payment limit.

Optional:
- `FIREBASE_DATABASE_URL` — Realtime Database URL, अगर auto-discovery काम न करे।
- `MAX_UPLOAD_MB`, `CACHE_DIR`, `CACHE_MAX_MB` — image storage configuration.

## सुरक्षा
Firebase Rules में `secrets` public read/write न दें। Service-account JSON और API key को GitHub, ZIP, Firebase client data या APK में न रखें। Payment credit सिर्फ backend की server-side status/amount verification के बाद होना चाहिए। पहले test environment में end-to-end जाँच करें; production payment से test न करें।
