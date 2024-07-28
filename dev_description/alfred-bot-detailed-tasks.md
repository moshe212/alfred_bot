# פירוט מורחב של משימות פיתוח לבוט אלפרד

## שלב 0: הכנה והגדרת סביבה

### 1. הגדרת סביבת פיתוח

#### א. התקנת Node.js ו-npm

1. גש לאתר הרשמי של Node.js (https://nodejs.org/)
2. הורד את הגרסה ה-LTS (Long Term Support) המתאימה למערכת ההפעלה שלך
3. הפעל את תוכנית ההתקנה ועקוב אחר ההוראות
4. לאחר ההתקנה, פתח טרמינל וודא את ההתקנה על ידי הרצת הפקודות:
   ```
   node --version
   npm --version
   ```

#### ב. הגדרת פרויקט Git ו-GitHub

1. התקן Git מ-https://git-scm.com/
2. צור חשבון ב-GitHub אם אין לך
3. צור repository חדש ב-GitHub
4. פתח טרמינל בתיקיית הפרויקט שלך
5. אתחל Git repository מקומי:
   ```
   git init
   git add .
   git commit -m "Initial commit"
   ```
6. חבר את ה-repository המקומי ל-GitHub:
   ```
   git remote add origin https://github.com/username/repo-name.git
   git push -u origin main
   ```

#### ג. הגדרת ESLint ו-Prettier

1. התקן את החבילות הנדרשות:
   ```
   npm install --save-dev eslint prettier eslint-config-prettier eslint-plugin-prettier
   ```
2. צור קובץ `.eslintrc.json`:
   ```json
   {
     "extends": ["eslint:recommended", "plugin:prettier/recommended"],
     "env": {
       "node": true,
       "es6": true
     },
     "parserOptions": {
       "ecmaVersion": 2018
     },
     "rules": {
       // כאן תוכל להוסיף כללים מותאמים אישית
     }
   }
   ```
3. צור קובץ `.prettierrc`:
   ```json
   {
     "singleQuote": true,
     "trailingComma": "es5"
   }
   ```

#### ד. הגדרת Jest לבדיקות יחידה

1. התקן Jest:
   ```
   npm install --save-dev jest
   ```
2. הוסף את הפקודה הבאה ל-`package.json` תחת `"scripts"`:
   ```json
   "test": "jest"
   ```

### 2. הגדרת חשבונות ושירותים חיצוניים

#### א. יצירת חשבון Supabase

1. גש ל-https://supabase.com/ וצור חשבון
2. צור פרויקט חדש
3. שמור את ה-API URL וה-API Key

#### ב. הגדרת פרויקט ב-Google Cloud Platform

1. גש ל-https://console.cloud.google.com/
2. צור פרויקט חדש
3. הפעל את ה-APIs הנדרשים (Calendar, Gmail)
4. צור credentials (OAuth 2.0 Client ID)

#### ג. רישום לשירות GreenAPI

1. גש ל-https://green-api.com/
2. צור חשבון והירשם לשירות
3. קבל את ה-API Key וה-Instance ID

### 3. הגדרת תשתית פרויקט

#### א. יצירת מבנה תיקיות בסיסי

1. פתח טרמינל בתיקיית הפרויקט
2. צור את מבנה התיקיות הבא:
   ```
   mkdir src config models controllers services utils
   touch src/app.js .env .gitignore
   ```

#### ב. הגדרת package.json

1. אתחל פרויקט npm:
   ```
   npm init -y
   ```
2. התקן תלויות ראשוניות:
   ```
   npm install express dotenv @supabase/supabase-js axios
   ```

#### ג. יצירת קובץ .env

הוסף את המשתנים הבאים ל-.env:

```
SUPABASE_URL=your_supabase_url
SUPABASE_KEY=your_supabase_key
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret
GREENAPI_INSTANCE_ID=your_greenapi_instance_id
GREENAPI_TOKEN=your_greenapi_token
```

#### ד. הגדרת Dockerfile בסיסי

צור קובץ `Dockerfile`:

```dockerfile
FROM node:14
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD [ "node", "src/app.js" ]
```

## שלב 1: תשתית בסיסית

### 4. הקמת שרת Express בסיסי

**צורך**: ליצור שרת web בסיסי שיוכל לטפל בבקשות HTTP.

**מימוש**:

1. פתח את `src/app.js`
2. הוסף את הקוד הבא:

```javascript
const express = require("express");
const cors = require("cors");
const bodyParser = require("body-parser");
require("dotenv").config();

const app = express();

app.use(cors());
app.use(bodyParser.json());

app.get("/health", (req, res) => {
  res.status(200).json({ status: "OK" });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

**הסבר**: קוד זה מגדיר שרת Express בסיסי עם middleware נפוץ (cors, body-parser) ונתיב בריאות פשוט. זה מהווה את הבסיס לכל הפונקציונליות העתידית של הבוט.

### 5. חיבור ל-Supabase

**צורך**: ליצור חיבור לבסיס הנתונים Supabase לאחסון ואחזור מידע.

**מימוש**:

1. צור קובץ `config/supabase.js`
2. הוסף את הקוד הבא:

```javascript
const { createClient } = require("@supabase/supabase-js");

const supabaseUrl = process.env.SUPABASE_URL;
const supabaseKey = process.env.SUPABASE_KEY;

const supabase = createClient(supabaseUrl, supabaseKey);

module.exports = supabase;
```

**הסבר**: קוד זה יוצר חיבור ל-Supabase באמצעות ה-URL וה-API Key שהגדרנו ב-.env. זה מאפשר לנו להשתמש ב-Supabase בכל מקום בפרויקט.

### 6. חיבור ל-GreenAPI

**צורך**: ליצור ממשק לשליחה וקבלה של הודעות WhatsApp.

**מימוש**:

1. צור קובץ `services/whatsappService.js`
2. הוסף את הקוד הבא:

```javascript
const axios = require("axios");

const instanceId = process.env.GREENAPI_INSTANCE_ID;
const token = process.env.GREENAPI_TOKEN;

const sendMessage = async (phoneNumber, message) => {
  try {
    const response = await axios.post(
      `https://api.green-api.com/waInstance${instanceId}/sendMessage/${token}`,
      {
        chatId: `${phoneNumber}@c.us`,
        message: message,
      }
    );
    return response.data;
  } catch (error) {
    console.error("Error sending WhatsApp message:", error);
    throw error;
  }
};

module.exports = { sendMessage };
```

**הסבר**: זה מגדיר פונקציה בסיסית לשליחת הודעות WhatsApp באמצעות GreenAPI. בהמשך נרחיב זאת לכלול קבלת הודעות וטיפול בהן.

### 7. הגדרת מערכת ניהול משתמשים

**צורך**: ליצור מערכת בסיסית לרישום והתחברות משתמשים.

**מימוש**:

1. צור קובץ `models/User.js`
2. הוסף את הקוד הבא:

```javascript
const supabase = require("../config/supabase");

const createUser = async (email, password) => {
  const { user, error } = await supabase.auth.signUp({
    email: email,
    password: password,
  });
  if (error) throw error;
  return user;
};

const loginUser = async (email, password) => {
  const { user, error } = await supabase.auth.signIn({
    email: email,
    password: password,
  });
  if (error) throw error;
  return user;
};

module.exports = { createUser, loginUser };
```

**הסבר**: זה מגדיר פונקציות בסיסיות לרישום והתחברות משתמשים באמצעות Supabase Auth. בהמשך נרחיב זאת לכלול ניהול פרופיל, שינוי סיסמה וכו'.

# המשך פירוט מורחב של משימות פיתוח לבוט אלפרד

## שלב 2: פונקציונליות ליבה

### 8. ניהול רשימת קניות

**צורך**: לאפשר למשתמשים ליצור, לערוך ולנהל רשימות קניות.

**מימוש**:

1. צור קובץ `models/ShoppingList.js`:

```javascript
const supabase = require("../config/supabase");

const createShoppingList = async (userId, name) => {
  const { data, error } = await supabase
    .from("shopping_lists")
    .insert({ user_id: userId, name: name });
  if (error) throw error;
  return data;
};

const addItemToList = async (listId, itemName) => {
  const { data, error } = await supabase
    .from("shopping_list_items")
    .insert({ list_id: listId, name: itemName });
  if (error) throw error;
  return data;
};

const getShoppingList = async (listId) => {
  const { data, error } = await supabase
    .from("shopping_list_items")
    .select("*")
    .eq("list_id", listId);
  if (error) throw error;
  return data;
};

module.exports = { createShoppingList, addItemToList, getShoppingList };
```

2. הוסף נתיבים ל-`app.js` לטיפול בבקשות WhatsApp הקשורות לרשימות קניות.

**הסבר**: קוד זה מגדיר פונקציות בסיסיות לניהול רשימות קניות ב-Supabase. בהמשך נוסיף לוגיקה לעיבוד פקודות WhatsApp לניהול הרשימות.

### 9. ניהול משימות

**צורך**: לאפשר למשתמשים ליצור, לערוך ולנהל משימות.

**מימוש**:

1. צור קובץ `models/Task.js`:

```javascript
const supabase = require("../config/supabase");

const createTask = async (userId, title, dueDate) => {
  const { data, error } = await supabase
    .from("tasks")
    .insert({ user_id: userId, title: title, due_date: dueDate });
  if (error) throw error;
  return data;
};

const completeTask = async (taskId) => {
  const { data, error } = await supabase
    .from("tasks")
    .update({ completed: true })
    .eq("id", taskId);
  if (error) throw error;
  return data;
};

const getUserTasks = async (userId) => {
  const { data, error } = await supabase
    .from("tasks")
    .select("*")
    .eq("user_id", userId)
    .order("due_date", { ascending: true });
  if (error) throw error;
  return data;
};

module.exports = { createTask, completeTask, getUserTasks };
```

2. הוסף נתיבים ל-`app.js` לטיפול בבקשות WhatsApp הקשורות למשימות.

**הסבר**: קוד זה מגדיר פונקציות בסיסיות לניהול משימות ב-Supabase. נשתמש בזה כבסיס לעיבוד פקודות משימות מ-WhatsApp.

### 10. אינטגרציה עם Google Calendar

**צורך**: לאפשר למשתמשים לנהל אירועים ביומן Google שלהם דרך הבוט.

**מימוש**:

1. התקן את החבילה הנדרשת: `npm install googleapis`
2. צור קובץ `services/googleCalendarService.js`:

```javascript
const { google } = require("googleapis");

const oauth2Client = new google.auth.OAuth2(
  process.env.GOOGLE_CLIENT_ID,
  process.env.GOOGLE_CLIENT_SECRET,
  process.env.GOOGLE_REDIRECT_URI
);

const addEvent = async (userToken, eventDetails) => {
  oauth2Client.setCredentials({ access_token: userToken });
  const calendar = google.calendar({ version: "v3", auth: oauth2Client });

  const event = {
    summary: eventDetails.summary,
    location: eventDetails.location,
    description: eventDetails.description,
    start: {
      dateTime: eventDetails.startTime,
      timeZone: "Your_Timezone",
    },
    end: {
      dateTime: eventDetails.endTime,
      timeZone: "Your_Timezone",
    },
  };

  try {
    const res = await calendar.events.insert({
      calendarId: "primary",
      resource: event,
    });
    return res.data;
  } catch (error) {
    console.error("Error adding event to Google Calendar:", error);
    throw error;
  }
};

module.exports = { addEvent };
```

**הסבר**: זה מגדיר פונקציה בסיסית להוספת אירוע ליומן Google של המשתמש. נצטרך להרחיב זאת כדי לכלול אותנטיקציה, רענון tokens, וטיפול בסוגים נוספים של פעולות יומן.

## שלב 3: NLP ואינטליגנציה

### 11. אינטגרציה עם שירות NLP

**צורך**: להבין את כוונת המשתמש מהודעות טקסט חופשיות.

**מימוש**:

1. התקן את החבילה הנדרשת: `npm install @google-cloud/language`
2. צור קובץ `services/nlpService.js`:

```javascript
const language = require("@google-cloud/language");

const client = new language.LanguageServiceClient();

const analyzeText = async (text) => {
  const document = {
    content: text,
    type: "PLAIN_TEXT",
  };

  try {
    const [result] = await client.analyzeEntities({ document });
    const entities = result.entities;
    const [syntaxResult] = await client.analyzeSyntax({ document });

    // כאן נוסיף לוגיקה לזיהוי כוונות על בסיס הישויות והתחביר
    // לדוגמה, זיהוי אם זו בקשה להוספת משימה, אירוע ליומן, וכו'

    return {
      entities: entities,
      syntax: syntaxResult,
      // נחזיר גם את הכוונה המזוהה
    };
  } catch (error) {
    console.error("Error in NLP analysis:", error);
    throw error;
  }
};

module.exports = { analyzeText };
```

**הסבר**: זה מגדיר שירות בסיסי לניתוח טקסט באמצעות Google's Natural Language API. נצטרך להרחיב זאת כדי לזהות כוונות ספציפיות רלוונטיות לפונקציונליות של הבוט.

### 12. פיתוח אישיות אלפרד

**צורך**: ליצור אינטראקציות מותאמות אישית עם הומור ורמזים לבאטמן.

**מימוש**:

1. צור קובץ `utils/alfredPersonality.js`:

```javascript
const batmanReferences = [
  "האם אתה זקוק לחליפה מיוחדת היום, אדוני?",
  "אני מקווה שהלילה יהיה שקט בגות'אם... אה, כלומר, בעיר שלך.",
  "האם להכין את הרכב המיוחד, או שנסתפק ברכב רגיל היום?",
];

const addHumor = (response, humorEnabled) => {
  if (!humorEnabled) return response;

  if (Math.random() < 0.1) {
    // 10% סיכוי להוסיף הומור
    const reference =
      batmanReferences[Math.floor(Math.random() * batmanReferences.length)];
    return `${response}\n\n${reference}`;
  }

  return response;
};

module.exports = { addHumor };
```

**הסבר**: זה מגדיר פונקציה פשוטה להוספת הומור לתגובות של אלפרד. נשתמש בזה בשילוב עם ה-NLP כדי ליצור תגובות מותאמות אישית.

## שלב 4: פונקציונליות מתקדמת

### 13. תזמון הודעות

**צורך**: לאפשר למשתמשים לתזמן הודעות לשליחה בזמן עתידי.

**מימוש**:

1. התקן את החבילה הנדרשת: `npm install node-schedule`
2. צור קובץ `services/schedulerService.js`:

```javascript
const schedule = require("node-schedule");
const whatsappService = require("./whatsappService");

const scheduleMessage = (phoneNumber, message, dateTime) => {
  const job = schedule.scheduleJob(dateTime, function () {
    whatsappService
      .sendMessage(phoneNumber, message)
      .then(() => console.log("Scheduled message sent successfully"))
      .catch((err) => console.error("Error sending scheduled message:", err));
  });

  return job;
};

module.exports = { scheduleMessage };
```

**הסבר**: זה מגדיר שירות לתזמון הודעות באמצעות `node-schedule`. נצטרך להרחיב זאת כדי לשמור את המשימות המתוזמנות ב-Supabase ולטפל בשינויים או ביטולים.

### 14. שמירת הערות והקלטות

**צורך**: לאפשר למשתמשים לשמור הערות טקסט והקלטות קוליות.

**מימוש**:

1. צור קובץ `models/Note.js`:

```javascript
const supabase = require("../config/supabase");

const saveTextNote = async (userId, content) => {
  const { data, error } = await supabase
    .from("notes")
    .insert({ user_id: userId, content: content, type: "text" });
  if (error) throw error;
  return data;
};

const saveAudioNote = async (userId, audioUrl) => {
  const { data, error } = await supabase
    .from("notes")
    .insert({ user_id: userId, content: audioUrl, type: "audio" });
  if (error) throw error;
  return data;
};

module.exports = { saveTextNote, saveAudioNote };
```

2. הרחב את `whatsappService.js` כדי לטפל בהקלטות קוליות.

**הסבר**: זה מגדיר פונקציות בסיסיות לשמירת הערות טקסט והקלטות. נצטרך להרחיב זאת כדי לטפל בקבצי אודיו מ-WhatsApp ולאחסן אותם ב-Supabase Storage.

## שלב 5: אבטחה ואופטימיזציה

### 15. יישום אמצעי אבטחה

**צורך**: להבטיח את אבטחת המידע והפרטיות של המשתמשים.

**מימוש**:

1. הוסף HTTPS:

   - בסביבת פיתוח, השתמש ב-`mkcert` ליצירת אישורים מקומיים.
   - בסביבת ייצור, השתמש ב-Let's Encrypt לאישורים חינמיים ואוטומטיים.

2. הצפנת מידע רגיש:

   - השתמש ב-`crypto` של Node.js להצפנת מידע רגיש לפני שמירה ב-Supabase.

3. הגדרת Row Level Security ב-Supabase:

   - הגדר מדיניות RLS בממשק הניהול של Supabase לכל טבלה.

4. ניהול תקין של tokens:
   - אחסן refresh tokens מוצפנים ב-Supabase.
   - נהל את חידוש ה-access tokens באופן אוטומטי.

### 16. אופטימיזציה וביצועים

**צורך**: לשפר את זמני התגובה והיעילות של הבוט.

**מימוש**:

1. הוסף caching:
   - התקן Redis: `npm install redis`
   - צור קובץ `services/cacheService.js`:

```javascript
const redis = require("redis");
const client = redis.createClient();

const setCache = (key, value, expiration) => {
  return new Promise((resolve, reject) => {
    client.setex(key, expiration, JSON.stringify(value), (err, reply) => {
      if (err) reject(err);
      resolve(reply);
    });
  });
};

const getCache = (key) => {
  return new Promise((resolve, reject) => {
    client.get(key, (err, data) => {
      if (err) reject(err);
      resolve(data ? JSON.parse(data) : null);
    });
  });
};

module.exports = { setCache, getCache };
```

2. אופטימיזציה של שאילתות Supabase:

   - השתמש ב-indexes על עמודות שנשאלות לעתים קרובות.
   - השתמש ב-`select()` כדי לבחור רק את העמודות הנדרשות.

3. הגדרת ניטור ביצועים:
   - התקן ושלב כלי ניטור כמו New Relic או Datadog.
   - צור דשבורד לניטור זמני תגובה, שימוש במשאבים, ושגיאות.

```javascript
// דוגמה לשימוש ב-New Relic
const newrelic = require("newrelic");

app.use((req, res, next) => {
  newrelic.setTransactionName(req.path);
  next();
});
```

**הסבר**: אמצעים אלו יעזרו לשפר את ביצועי הבוט ולזהות צווארי בקבוק. הcaching יפחית עומס מהדאטאבייס, אופטימיזציה של שאילתות תשפר זמני תגובה, וניטור יאפשר לנו לזהות ולטפל בבעיות ביצועים.

## שלב 6: בדיקות ותיעוד

### 17. כתיבת בדיקות

**צורך**: להבטיח את אמינות ויציבות הקוד.

**מימוש**:

1. כתוב בדיקות יחידה עם Jest:

```javascript
// __tests__/models/Task.test.js
const { createTask, completeTask } = require("../../models/Task");
const supabase = require("../../config/supabase");

jest.mock("../../config/supabase");

describe("Task Model", () => {
  test("createTask creates a new task", async () => {
    supabase.from.mockReturnValue({
      insert: jest
        .fn()
        .mockResolvedValue({ data: { id: 1, title: "Test Task" } }),
    });

    const task = await createTask(1, "Test Task", "2023-12-31");
    expect(task).toEqual({ id: 1, title: "Test Task" });
  });

  // Add more tests...
});
```

2. הגדר תהליך CI/CD ב-GitHub Actions:

```yaml
# .github/workflows/ci.yml
name: CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Use Node.js
        uses: actions/setup-node@v2
        with:
          node-version: "14"
      - run: npm ci
      - run: npm test
```

### 18. תיעוד

**צורך**: לספק מידע ברור למפתחים ומשתמשים.

**מימוש**:

1. כתוב README.md מקיף:

```markdown
# בוט אלפרד

בוט WhatsApp לניהול פרודוקטיביות, בהשראת אלפרד מבאטמן.

## התקנה

1. Clone the repository
2. Run `npm install`
3. Copy `.env.example` to `.env` and fill in your credentials
4. Run `npm start`

## שימוש

[הסבר על כיצד להשתמש בבוט]

## פיתוח

[מידע למפתחים על מבנה הפרויקט וכיצד לתרום]

...
```

2. תעד את ה-API (פנימי וחיצוני) עם JSDoc:

```javascript
/**
 * Creates a new task for a user.
 * @param {number} userId - The ID of the user creating the task.
 * @param {string} title - The title of the task.
 * @param {string} dueDate - The due date of the task in ISO format.
 * @returns {Promise<Object>} The created task object.
 */
const createTask = async (userId, title, dueDate) => {
  // Implementation...
};
```

3. צור מדריך למשתמש:
   - הסבר על כל הפקודות הזמינות
   - תן דוגמאות לשימוש
   - כלול מידע על פרטיות ואבטחה

## שלב 7: השקה ותחזוקה

### 19. הכנה להשקה

**צורך**: להבטיח שהבוט מוכן לשימוש בסביבת ייצור.

**מימוש**:

1. הגדר סביבת הרצה (staging):

   - השתמש בשירות כמו Heroku או AWS לסביבת staging.
   - וודא שכל המשתנים הסביבתיים מוגדרים נכון.

2. בצע בדיקות עומס:

   - השתמש בכלי כמו Apache JMeter לסימולציה של עומס גבוה.

3. הכן תוכנית לגיבוי ושחזור נתונים:
   - הגדר גיבויים אוטומטיים של Supabase.
   - צור ותעד נוהל לשחזור מגיבוי.

### 20. תמיכה ושיפור מתמיד

**צורך**: להבטיח שהבוט ממשיך לפעול כראוי ומשתפר לאורך זמן.

**מימוש**:

1. הגדר מערכת לאיסוף משוב משתמשים:

   - הוסף פקודה בבוט לשליחת משוב.
   - שקול שימוש בכלי כמו Typeform לסקרי משתמשים.

2. תכנן תהליך לעדכונים ושדרוגים:

   - קבע מחזור שחרור קבוע (למשל, כל שבועיים).
   - השתמש בגרסאות סמנטיות לתיוג שחרורים.

3. הגדר מדדי ביצוע (KPIs) למעקב:
   - מספר משתמשים פעילים
   - זמן תגובה ממוצע
   - שיעור השלמת משימות
   - דירוג שביעות רצון משתמשים

**סיכום**:
זהו תהליך הפיתוח המלא של בוט אלפרד. כל שלב בנוי על הקודמים לו, ויחד הם יוצרים מערכת מקיפה ויעילה. זכור שפיתוח הוא תהליך איטרטיבי, ויתכן שתצטרך לחזור ולשפר חלקים שונים לאורך הדרך. שמירה על גמישות ופתיחות לשינויים היא מפתח להצלחת הפרויקט.
