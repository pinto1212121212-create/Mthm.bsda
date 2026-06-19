# חיבור Firebase (סנכרון ענן מוצפן E2E)

הענן שומר **רק את הכספת המוצפנת** (blob). Firebase/Google מאחסנת אך לא יכולה לקרוא — אין לה את הקוד שלך.

## שלבי הקמה (פעם אחת)

1. **צור פרויקט** ב‑https://console.firebase.google.com (כבר עשית — "Yomny").
2. **Authentication** → Get started → Sign-in method → הפעל **Google** → Save.
3. **Firestore Database** → Create database → **Production mode** → בחר אזור → Enable.
4. **Authorized domains**: Authentication → Settings → Authorized domains → הוסף את הדומיין של GitHub Pages
   (לדוגמה `pinto1212121212-create.github.io`).
5. **Web app config**: Project Settings (⚙️) → "Your apps" → Web (`</>`) → רשום אפליקציה →
   העתק את אובייקט ה‑`firebaseConfig`.
6. **כללי אבטחה**: Firestore → Rules → הדבק את הכללים למטה → Publish.

## כללי האבטחה (Firestore Rules)

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /vaults/{uid} {
      allow read, write: if request.auth != null && request.auth.uid == uid;
    }
  }
}
```

זה מבטיח שכל משתמש ניגש **רק** למסמך הכספת שלו (`vaults/<uid>`).

## באפליקציה

1. פתח את כפתור ה‑☁️ בסרגל העליון → הדבק את ה‑`firebaseConfig` → **חבר ל‑Firebase**.
2. **התחבר עם Google**.
3. זהו — מעכשיו כל שינוי מגובה ומסונכרן.
4. במכשיר חדש: התחבר עם אותו חשבון Google → הזן את **הקוד / מפתח השחזור** → היומן שלך שם.

## הערות אבטחה

- ה‑`apiKey` של Firebase **מותר** להיות גלוי — הוא מוגן ע״י כללי האבטחה. אל תשתף מפתחות `service account`.
- הצופן (DEK) לעולם לא נשלח לענן — רק ה‑blob המוצפן וה‑META העטוף. בלי הקוד אי אפשר לפענח.
