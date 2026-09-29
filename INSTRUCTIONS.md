# הוראות התפעול, דשבורד ומערכת לידים

הקובץ הזה הוא מקור האמת לשתי הרוטינות בענן שמפעילות את albertleviart-cmd/clart-dashboard. עדכון התנהגות נעשה בעריכת הקובץ הזה ופוש לגיטהאב, לעולם לא בעריכת הרוטינה עצמה.

- **חלק א'** למטה הוא של clart-leads-sync בלבד: רצה פעם ביום ב-07:03 שעון ישראל, על מודל Opus, כותבת ל-leads.json ול-reports/.
- **חלק ב'** למטה הוא של clart-dashboard-sync בלבד: רצה שבע פעמים ביום בשעות קבועות (06:00, 09:00, 12:00, 15:00, 18:00, 21:00, 23:00 שעון ישראל), על מודל Sonnet, כותבת ל-data.json בלבד.

כל רוטינה קוראת ומבצעת רק את החלק שלה. אף אחת מהן לא נוגעת ב-index.html בשום מצב.

---

# חלק א': סריקת לידים יומית (clart-leads-sync בלבד)

# כלל ברזל: לעולם לא לשלוח הודעה ללקוח

**אסור לשלוח מייל, SMS, וואטסאפ או כל הודעה לאף לקוח. שום דבר לא יוצא ללקוח בלי שאלברט לחץ שלח בעצמו.**

אסור להשתמש בכלים: `send_message`, `reply`, `forward`, `send-sms`, `send-message`, `send-bulk-messages`, `send-group-message`, `send-whatsapp-freeform`, `send-whatsapp-template`, `send-conversation-message`, `make-call`, `generate-ai-reply`, `GMAIL_SEND_EMAIL`, `GMAIL_SEND_DRAFT`, `GMAIL_REPLY_TO_THREAD`.

מה שמותר לכתוב, וזה הכל:
1. לוח Leads במנדיי
2. טיוטות בגמייל עם `create_draft` בלבד
3. משימות ב-Quo עם `create-task` בלבד
4. הקבצים `leads.json` ו-`reports/YYYY-MM-DD.md` בריפו הזה

**אם יש ספק אם פעולה שולחת משהו ללקוח, לא לעשות אותה ולרשום בדוח.**

---

# מזהים

**Monday** לוח Leads `2037430545`. עמודות:
- `lead_status` סטטוס, `text_mm0f3w1k` Notes, `lead_email`, `lead_phone`
- `color_mm5pwdxp` Last Contact Channel: QUO / WhatsApp / SMS פרטי / Email / Instagram / Facebook / Other / Phone Call / SMS עיסקי
- `date_mm5fyywa` Last Contacted, `date_mm5p2y2e` Next Action Date
- `numeric_mm5f7m9m` Deal Value, `date_mm7kecgd` **Close Date**, `numeric_mm7kz234` **Amount Paid**
- `date_mm5pc346` Deposit Date, `text_mksv58se` why connected, `text_mm3568ct` Budget Range
- `boolean_mm6e3v60` Consultation Booked, קבוצת New Leads: `group_mm5d4w50`

**מלכודות כתיבה למנדיי:** `lead_email` דורשת אובייקט `{"email":"a@b.com","text":"a@b.com"}` ולא מחרוזת. תיבת סימון דורשת `{"checked":"true"}` ולא `true`. `lead_phone` מקבלת מחרוזת ספרות רגילה.

**Quo** תיבה `+19296422750`. התיבה `+12133548401` ריקה, לא לבזבז עליה קריאה. משתמש להקצאת משימות: `USfgZruqEw`.

**Gmail עסקי** חשבון `fineart@albertlevi.com`, דרך קונקטור Gmail. `search_threads` עם הפרמטר `query` בלבד.

**Gmail פרטי** חשבון `albertleviart@gmail.com`, **רק דרך Composio**, `COMPOSIO_MULTI_EXECUTE_TOOL` עם `GMAIL_FETCH_EMAILS`, תמיד `account: "gmail_encave-crib"`, ארגומנטים `{"query":"...","max_results":60,"verbose":false,"include_payload":false}`. בלי verbose ובלי payload, אחרת הפלט עצום. גוף הודעה בודדת: `GMAIL_FETCH_MESSAGE_BY_MESSAGE_ID` עם format full, השדה השימושי `messageText`. טיוטה פרטית: `GMAIL_CREATE_EMAIL_DRAFT` עם `thread_id` ובלי `subject`, ו-cc חייב מערך.

**Shopify** חנות albertlevi.com. `graphql_query` על orders.

**Autocalls** `list-calls`. שיחה עם status completed ומשך מעל 20 שניות היא שיחה אמיתית. `answered_by: "machine"` הוא תא קולי. מענה אוטומטי שמבקש להשאיר שם הוא screening ולא שיחה.

**Google Analytics** דרך Composio. toolkit slug `google_analytics` **עם קו תחתון**, account `google_analytics_gault-yoop`. נכס `properties/415263029` בשם Albert art shopify, וזה הנכס לסריקה. `properties/440744270` הוא אתר ישראלי נפרד ולא נכנס.

**Google Ads** דרך Composio. slug `googleads` **בלי קו תחתון**, account `googleads_stower-chinny`, customer `7282991915`.

**Meta Ads** חשבון `400319919198934` בדולרים. יש גם `2111326855852065` בשקלים, לציין אם יש בו הוצאה.

**Stripe** מחובר ומאומת ב-28.09.2026. `stripe_api_read` עם `stripe_api_operation_id` `GetCharges`, `stripe_context` `acct_1SM0Z937dfJIOq2B`, `livemode` true, ו-`parameters` **`{"limit":5}`**. **סכומים חוזרים ביחידת המטבע הקטנה, סנטים או אגורות, לחלק ב-100.** השדה `billing_details.email` הוא איך מצליבים לליד, ו-`created` הוא timestamp יוניקס. אם הכלי אינו זמין בריצה מסוימת, לרשום בדוח שהוא מנותק ולהמשיך, אל תיתקע.

**למה 5 ולא 100, ואל תגדיל את זה בלי סיבה.** הסריקה ההיסטורית המלאה בוצעה פעם אחת ב-28.09.2026 ותוצאותיה למטה, ולכן אין שום צורך לחזור עליה. הקצב האמיתי הוא כ-13 חיובים בחודש והיום העמוס ביותר בספטמבר החזיק שני חיובים, כלומר חלון של 5 חיובים מכסה כשבוע עד עשרה ימים אחורה ולא יפספס תשלום בין ריצות. **לפני שמגדילים, לבדוק אם החיוב החמישי כבר מלפני הריצה הקודמת. אם כן, החלון מספיק.** להגדיל ל-100 רק בשתי סיבות: חיוב שאי אפשר לשייך לאף ליד, או השוואה חודשית מלאה שאלברט ביקש במפורש.

**מה שכן התברר בסריקה ההיסטורית, ואינו נכון יותר:** ההוראה הקודמת אמרה שהקונקטור מתעלם מפילטרי תאריך ומחזיר רק 10 חיובים אחרונים. **זה לא נכון**, `limit` עובד והוא החזיר 100 חיובים עד מרץ 2026 עם `has_more` true.

**פער פתוח שנמצא ב-28.09 ואינו נסרק יותר אוטומטית, לכן הוא רשום כאן כדי שלא ייעלם.** 7,946 דולר שנכנסו ב-Stripe בספטמבר ואינם מיוצגים באף רשומה עם Close Date של ספטמבר. מתוכם 5,396 משבעה משלמים שאין להם רשומה בלוח בכלל: Caryn Kelhoffer `kcycle64@gmail.com` 1,325 ב-23.09, Elizabeth Underwood `elizabeth@keithteam.com` 800 ב-16.09, Rebecca Gold `rmgold15@gmail.com` 1,080 ב-09.09, Carolyn Hockstein `carolynbgh@gmail.com` 825 ב-07.09, Lynn Hellerstein `drh@lynnhellerstein.com` 229 ב-07.09, Nathaniel Amirian `nathanielamirianesq@gmail.com` 387 ב-04.09, Yocheved Keren `y.keren@student.fdu.edu` 750 ב-01.09. ועוד 2,550 של Henry `henry@twinflames.co` שיש לו רשומה בלי Close Date. **אי אפשר לדעת מהנתונים אם אלה עסקאות חדשות או השלמות על עסקאות מחודשים קודמים, ולכן לא נרשם כלום ולא נוגעים במחזור. זו החלטה של אלברט. כל עוד הוא לא הכריע, להזכיר את השורה הזאת בדוח פעם בשבוע ולא בכל יום.**

**תבנית המקדמה מאומתת.** Stripe הראה שלוש עסקאות בתבנית 850 ואחר כך 2,550, כלומר בדיוק 25 ו-75 אחוז מ-3,400: Amy Yaffe, Adam Carl ו-Henry. זה מחזק את כלל 25 האחוז לזיהוי מקדמה, וגם את ה-3,900 של Meisel שנגזר מ-975.

**וואטסאפ** אין API ואין מסך בענן. **לא ניתן לסרוק, לעולם.** לרשום בדוח שורה קבועה שהוא לא נסרק. שם נסגרים הקומישנים הגדולים, כ-15,000 דולר בחודש שלא מופיעים בשום ערוץ אחר, ולכן לציין שכדאי סריקה ידנית שבועית.

---

# איך רושמים כסף, זה הכלל הקריטי

**אלברט מודד מחזור לפי מתי העסקה נסגרה, לא לפי מתי הכסף נכנס.**

עסקה על 3,000 דולר עם מקדמה 750: **החודש נספר 3,000, לא 750.** לקוח שסגר לפני שלושה חודשים ומשלים עכשיו יתרה: **החודש נספר אפס**, כי ה-3,000 נספרו אז. לספור שוב זו ספירה כפולה.

| עמודה | מה מחזיקה | מתי נכתבת |
|---|---|---|
| `numeric_mm5f7m9m` Deal Value | שווי העסקה המלא, לא גובה החיוב | פעם אחת בסגירה, אחר כך לא נוגעים |
| `date_mm7kecgd` Close Date | מתי הלקוח אישר את הקנייה | פעם אחת בסגירה, תשלום השלמה לא מזיז אותה |
| `numeric_mm7kz234` Amount Paid | כמה כסף נכנס בפועל, מצטבר | בכל תשלום |

יתרה לגבייה היא Deal Value פחות Amount Paid.

**האלגוריתם על כל תשלום:**
1. מצא את הליד לפי מייל וטלפון.
2. **יש לו Close Date מחודש קודם?** זו השלמה. עדכן **רק** Amount Paid. אל תיגע ב-Deal Value ולא ב-Close Date, ואל תדווח כעסקה חדשה. בדוח לשורה "כסף שנכנס, לא מחזור חדש".
3. **אין Close Date?** עסקה חדשה. Deal Value מקבל את שווי העסקה המלא **מתוך השיחה**, Close Date את תאריך הסגירה, Amount Paid את מה שנכנס.

**מחזור חודשי מחושב רק על רשומות עם Close Date באותו חודש.** זה מנטרל אוטומטית הצעות מחיר שלא נסגרו ויושבות ב-Deal Value. **אל תסכום את Deal Value בלי לסנן לפי Close Date.**

**איך מזהים מקדמה מול תשלום מלא בלי לשאול את אלברט.** הוא אמר במפורש שהוא לא יכול לכתוב את זה כל פעם, זה חייב לבוא מההקשר:
1. **חשבון, הסימן החזק ביותר:** החיוב שווה למחיר שהוצע כולל הנחה שהוזכרה, זה **תשלום מלא**.
2. **החיוב הוא שבר נקי של המחיר**, בעיקר 25 או 50 אחוז, זו **מקדמה**. אלברט עובד ב-25 אחוז כברירת מחדל.
3. **מילים:** deposit, down payment, balance, the rest, מקדמה, יתרה, כולן מקדמה. "Payment complete", "I paid today" עם הסכום המלא, או בקשת כתובת משלוח מיד אחרי, הן תשלום מלא.
4. **הזמנת שופיפיי היא תמיד תשלום מלא.** קופה גובה את כל הסכום.
5. **מקור או קומישן:** מקדמות נפוצות. **פרינט או מרצ'נדייז:** כמעט תמיד תשלום מלא.
6. סטטוס Deposit Paid או Deposit Pending הם מקדמה בהגדרה.

**אם אחרי כל אלה אתה לא בטוח, אל תנחש.** רשום Amount Paid, השאר Deal Value ו-Close Date כמו שהם, ורשום בדוח "לא ברור אם מקדמה או תשלום מלא" עם שם וסכום. **עדיף חור מסומן מאשר מחזור שגוי.**

---

# מה לעשות בכל ריצה

## 1. משוך מכל הערוצים את מה שקרה ב-24 השעות האחרונות
Quo, Gmail עסקי, Gmail פרטי, Shopify, Stripe אם מחובר, Autocalls, Meta Ads, Google Ads, Google Analytics.

**Quo הוא החריג ונסרק 14 יום אחורה.** `fetch-messages` על `+19296422750` עם `excludeDoneConversations: true` ו-`createdAfter` של 14 יום. חלון של 24 שעות מפספס בדיוק את הלידים ששאלו שאלה ונשכחו, וזה הנזק הגדול.

**Gmail עסקי, שלושה חיפושים:**
- `newer_than:2d` למה שנכנס אתמול
- `buy.stripe.com newer_than:7d` ללינקי תשלום
- `newer_than:30d category:primary -from:no-reply -from:noreply` **חובה ולא אופציונלי.** חלון של יומיים מפספס שיחה חיה ששתקה שלושה ימים, ואלה בדיוק הלידים הגדולים. חפש שרשורים שבהם אדם אמיתי כתב על יצירה, מחיר, גודל, קומישן, ביקור או הזמנה, והצלב כל כתובת מול `lead_email` במנדיי. את הסוופ הזה עושים **גם בתיבה הפרטית**.

## 1ב. משוך לידים שנוצרו במנדיי עצמו ב-48 השעות האחרונות
`get_board_items_page` עם פילטר על העמודה הווירטואלית `__creation_log__`, אופרטור `within_the_last`, compareValue `["DAYS",2]`, orderBy אותה עמודה desc.
**למה זה חייב:** ליד מטופס האתר נכנס למנדיי אוטומטית תוך שנייה, ואם הסריקה רק מצליבה מול הערוצים, ליד בלי פעילות ב-Quo או באימייל לא יעלה בדוח בכלל. קרא לכל אחד גם `text_mksv58se` ו-`text_mm3568ct`.

**שים לב:** פניות מטופס הקשר של שופיפיי מגיעות מ-`mailer@shopify.com` ו**אינן** נכנסות אוטומטית למנדיי, בשונה מפניות powerfulform. לבדוק אותן ידנית ולפתוח רשומה.

## 2. הצלב לפי מייל וטלפון, לא לפי שם
שמות פרטיים כמו David, Lisa, Elizabeth חוזרים הרבה ויוצרים התאמות שגויות.

## 3. עדכן את לוח Leads
- **הערות:** לעולם אל תדרוס. קרא קודם את `text_mm0f3w1k` ואז כתוב מחדש עם התוספת בסוף: `<הערה קיימת> || עדכון DD.MM מ<מקור>: <מה קרה>`
- **סטטוס:** שנה רק אם יש ראיה שהלקוח הגיב. הודעה יוצאת, תמונה שאלברט שלח, או תא קולי אינם תגובה. אם הגיב והסטטוס Unreachable או New Lead, שנה ל-Conversation Active.
- **כסף:** לפי פרק "איך רושמים כסף" למעלה.
- **ערוץ ותאריך:** `color_mm5pwdxp` ו-`date_mm5fyywa` לפי הקשר האחרון בפועל.
- **תאריך פעולה:** לכל ליד שהגיב או שמחכה לתשובה, `date_mm5p2y2e`, ברירת מחדל יום העסקים הבא.
- **לא רלוונטי:** אם אמר במפורש שאין תקציב או לא מעוניין, סטטוס מתאים ובלי תאריך פעולה.

## 4. משלם שאינו ליד: אל תיצור רשומה
רשום אותו בדוח תחת "משלמים שאינם לידים" כדי שאלברט יחליט. **משלם הוא היוצא מן הכלל, לליד נכנס כן יוצרים רשומה.**

## 4ב. ליד נכנס חדש שאינו במנדיי: צור לו רשומה
**סף הכניסה, שלושתם חייבים:**
א. אדם אמיתי שכתב בעצמו. לא no-reply, לא ניוזלטר, לא קוד אימות, לא מענה אוטומטי, ולא ספק או עסק (Printify, Mercury, Awin, Shopify, Park West, Dropbox Sign, Autocalls, Upwork, UPS).
ב. **כוונה שקשורה ליצירה:** שאלה על מחיר, גודל, משלוח, קומישן, זמינות, או התעניינות מפורשת ביצירה. **ברכת חג, תודה, מזל טוב או לייק אינם ליד.**
ג. **בדיקת כפילות לפני יצירה, הכי חשוב.** חפש את המייל וגם את הטלפון בנפרד עם `contains_text` על `lead_email` ועל `lead_phone`. בלוח מעל 1,600 רשומות וכבר הרבה כפילויות. **בספק, אל תיצור ורשום בדוח.** לא להצליב לפי שם פרטי.

**מה למלא** ב-`create_item` בקבוצה `group_mm5d4w50`: `name`, `lead_email`, `text_mksv58se` עם משפט או שניים במילים שלו, `color_mm5pwdxp` לפי הערוץ, `date_mm5fyywa` תאריך הפנייה, `date_mm5p2y2e` יום העסקים הבא, `lead_phone` אם יש, ו-`lead_status` ל-**Conversation Active**.

**למה Conversation Active ולא New Lead.** האוטומציה `Monday Status New Lead Calls` מופעלת רק על New Lead ברשומה שנוצרה ב-10 הדקות האחרונות. **Conversation Active אינו ברשימת הסטטוסים שמתירים חיוג**, ולכן אפשר למלא טלפון בלי שהבוט יתקשר תוך דקות. זה גם נכון לגופו: מי שכתב ביוזמתו אינו ליד קר.

## 5. בדוק כפילויות
אותו מייל או טלפון בשתי רשומות. **רשום בדוח, אל תמחק.**

## 6. הכן טיוטות ומשימות
ראה הפרקים הבאים.

---

# רצף השיחות של Autocalls, מה עוצר ומה לא

**סטטוס Unreachable אינו עוצר שיחות, הוא דווקא מתיר אותן.** הבדיקה לפני חיוג מאשרת רק שלושה סטטוסים: `New Lead`, `Unreachable`, `Contact Later`. כל סטטוס אחר עוצר.

מה שעוצר את הרצף: שיחה אמיתית עם אדם, do_not_call, ייעוץ שנקבע (`boolean_mm6e3v60`), תאריך מקדמה (`date_mm5pc346`), או סטטוס מחוץ לשלושה.

מגבלות: עד 3 נסיונות ביום, חלון 5 ימים, מקסימום 15 נסיונות, ורק 07:00 עד 21:00 שעון ניו יורק.

**משמעות:** כשאתה מתקן סטטוס מ-Unreachable ל-New Lead **אתה לא משנה שום דבר בהתנהגות החיוג**, שניהם מתירים. התיקון הוא לדיווח בלבד. **אל תכתוב בדוח שעצרת שיחות על ידי שינוי סטטוס.**
להוצאת ליד מהרצף צריך סטטוס מחוץ לשלושה, למשל Conversation Active. זו פעולה שמשנה מערכת חיה, **להמליץ עליה בדוח ולא לעשות לבד**, חוץ ממקרה שבו הלקוח באמת הגיב בערוץ אחר, שאז Conversation Active נכון לגופו.

---

# טיוטות מייל

לכל ליד בפרק "מה דורש תשובה היום", הכן טיוטה אחת.

- תמיד עם `replyToMessageId` של ההודעה האחרונה בשרשור. ליד חדש בלי שרשור הוא היחיד שנפתח כמייל חדש.
- שמור `cc` של כל מי שהיה בשרשור. בן או בת זוג נשארים ב-cc.
- **לפני כתיבה קרא את השרשור עצמו עם `get_thread` ב-PLAIN_TEXT.** אל תסתמך על ה-snippet, הוא חותך.
- **מלכודת: ל-`update_draft` אין `replyToMessageId`**, ולכן עדכון גוף של טיוטת תגובה מנתק אותה מהשרשור. לתיקון נוסח: `delete_draft` ואז `create_draft` מחדש. לבדוק שה-threadId שחוזר הוא של השרשור המקורי.
- **באיזו תיבה:** בזו שהשיחה מתנהלת בה. שרשור בתיבה הפרטית מקבל טיוטה פרטית דרך Composio, שרשור עסקי מקבל טיוטה עסקית. **לא להעביר שיחה בין תיבות.** ליד חדש בלי שרשור, ברירת המחדל היא העסקית בגלל החתימה והברנדינג.

**סגנון, זה קריטי:**
- **בלי מקף ארוך, אף פעם.**
- אנגלית פשוטה וחמה של בן אדם, לא AI. משפטים קצרים. בלי delve, elevate, unlock, journey, testament.
- לפתוח בעניין עצמו, לא ב-I hope this email finds you well.
- להתייחס ספציפית למה שהלקוח כתב, בשמו ובמילים שלו. אם מסר מידות, להשתמש במידות שלו.
- **דבר אחד לכל מייל, ושאלה אחת ברורה בסוף.** לא שלוש שאלות.
- לחתום `Best, Albert`. בלי חתימה ארוכה, גמייל מוסיף אותה.
- אם סיפר משהו אישי, מחלה במשפחה או חג, להתייחס במשפט אחד בהתחלה ואז לעניין.

**מה אסור להמציא:** מחיר, תאריך אספקה, מספר מעקב, לינק תשלום, מידה במלאי, או תוכן מסמך שלא קראת. אם חסר נתון, **סוגריים מרובעים עם הנחיה**, למשל `[אלברט: להדביק כאן את המחיר]`, ולרשום בדוח שהטיוטה מחכה להשלמה. **עדיף טיוטה עם חור מסומן מאשר טיוטה עם מחיר שגוי.**

**לפני שמבטיחים משהו על החנות, תבדוק בשופיפיי.** אם ליד אומר שקוד הנחה לא עבד, לבדוק עם `codeDiscountNodeByCode` וגם את הסטטוס והקולקשנים של המוצר. **בגלל מיגרציית הקטלוג יש מוצרים ישנים ב-DRAFT עם מחיר ישן ותאומים חדשים ב-ACTIVE במחיר אחר, וקוד שמוגבל לקולקשן אחד נכשל בשקט על כל מה שמחוץ לו.** לרשום את שורש התקלה בדוח.

**מי לא מקבל טיוטה:** מי שאלברט כבר ענה לו והכדור אצל הלקוח, מי שרק בירך לחג בלי כוונת קנייה, ומי שאמר במפורש שאינו מעוניין. **אל תייצר טיוטת תזכורת פחות מ-48 שעות אחרי שאלברט כתב.**

---

# משימות Quo

ל-Quo אין API של טיוטות, והכלים לשליחה אסורים. צור משימה שמחזיקה את הנוסח, והעתק את הנוסח גם לדוח.

1. `fetch-messages` על `+19296422750`, `excludeDoneConversations: true`, `createdAfter` של **14 יום**.
2. זהה שיחות שבהן **ההודעה האחרונה היא incoming**, כלומר הלקוח דיבר אחרון ולא קיבל תשובה. **עדיפות עליונה**, במיוחד מעל 3 ימים או שאלה ישירה על מחיר או גודל.
3. זהה שיחות שבהן אלברט שלח הצעה או שאלה ועברו מעל 7 ימים בלי תגובה. נגיעה אחת מנומסת שסוגרת לכאן או לכאן.
4. לכל אחת `create-task` עם `conversationId` (CN...), `assignedTo: USfgZruqEw`, `dueDate` יום העסקים הבא.
   - **title:** שם הליד, הנושא, וכמה זמן מחכה. בעברית.
   - **description:** פסקת הקשר בעברית עם ציטוט ההודעה האחרונה של הלקוח, ואחריה `נוסח מוצע לשליחה:` והטקסט באנגלית מוכן להעתקה. אותם כללי סגנון של הטיוטות.
5. **לפני יצירה הרץ `list-tasks` ובדוק שאין משימה פתוחה לאותה שיחה.** אין ליצור כפילות בריצות עוקבות. אם קיימת ופתוחה, רק לציין בדוח שהיא ממתינה.
6. **מי שלא מקבל משימה:** מי שאמר שאין תקציב או אינו מעוניין ואלברט כבר הגיב באלגנטיות, קודי אימות ומספרי מערכת, ומי שאלברט כתב לו בפחות מ-48 שעות.

**אם ליד מנהל את השיחה גם במייל וגם ב-SMS, תן לו טיוטת מייל או משימת Quo, לא את שתיהן**, לפי הערוץ שבו השיחה התנהלה לאחרונה. שתי נגיעות באותו יום בשני ערוצים נראה לחוץ.

**אם לא נוצרה שום משימה, לציין בדוח שהחלון של 14 יום נסרק ולא נמצאה שיחה תלויה.** אל תשתוק על זה.

---

# הפלט: שני קבצים בריפו

## leads.json
זה מה שהדשבורד קורא. מבנה:

```json
{
  "updatedAt": "ISO עם offset +03:00",
  "scanDate": "YYYY-MM-DD היום שהריצה מכסה",
  "sources": {"monday":{"status":"ok|error","note":""}, "quoMessages":{}, "gmailBusiness":{}, "gmailPrivate":{}, "shopify":{}, "stripe":{}, "autocalls":{}, "calendar":{}},
  "needsAnswer": [{"name":"", "why":"", "channel":"", "waitingDays":0, "urgency":"high|medium|low"}],
  "newYesterday": [{"name":"", "source":"", "detail":""}],
  "replied": [{"name":"", "channel":"", "said":""}],
  "revenue": {"monthToDate":0, "cashNotTurnover":0, "openBalances":0, "currency":"USD"},
  "draftsPrepared": [{"name":"", "inbox":"business|private", "gist":"", "needsInput":false}],
  "quoTasks": [{"name":"", "waitingDays":0, "asked":""}],
  "mondayUpdated": {"updated":0, "created":0, "details":[]},
  "faults": [{"what":"", "severity":"high|medium|low"}],
  "whatsappNote": "לא נסרק, אין API. סריקה ידנית אחרונה: YYYY-MM-DD",
  "meetings": [{"when":"", "who":""}]
}
```

**לשדות שנכשלו, קח מה-leads.json הישן, אל תמחק.** קרא אותו עם Read לפני שאתה כותב.

## reports/YYYY-MM-DD.md
דוח קריא בעברית, **לא יותר מ-25 שורות, בלי מקף ארוך**. מבנה:

`# דוח לידים יומי | DD.MM.YYYY` ואז:
**מה נכנס אתמול** לידים חדשים, תשלומים וסכומים.
**מי הגיב** שם, ערוץ, ומה כתב בקצרה.
**מה דורש ממך תשובה היום** רשימה ממוקדת, מי מחכה ולמה. **זה החלק הכי חשוב.**
**פגישות היום ומחר**.
**כסף** מחזור החודש לפי Close Date בלבד, ובשורה נפרדת "כסף שנכנס, לא מחזור חדש", ואחר כך יתרות פתוחות ולינקי תשלום שלא שולמו מעל 3 ימים. **אל תערבב בין השלושה.**
**תנועה ופרסום** סשנים אתמול מול ממוצע 7 ימים, הוצאת מטא וגוגל מול הכנסה בפועל משופיפיי, ערוץ שקרס או זינק ביותר מ-40 אחוז, אחוז Unassigned אם מעל 15, ו-Organic Search כמדד SEO. שלוש עד ארבע שורות.
**תקלות** כפילויות, טלפון או מייל שגוי, סטטוס שלא מתיישב עם המציאות, וקונקטור שנפל.
**מה עודכן במנדיי** מספר רשומות ומה שונה.
**טיוטות שהוכנו** שורה לכל טיוטה, למי, באיזו תיבה, ומה העיקר, ואיזו מחכה להשלמה.
**משימות Quo שנוצרו** שורה לכל אחת.
**וואטסאפ** שורה קבועה שהוא לא נסרק ומתי הייתה הסריקה הידנית האחרונה.

אם יום שקט ואין שום דבר, כתוב שורה אחת שאומרת את זה. **אל תמציא תוכן.**

אם הקובץ לאותו תאריך קיים, אל תדרוס, הוסף בסופו `## ריצה נוספת HH:MM`.

## פרסום
```
git add leads.json reports/
git commit -m "leads scan <YYYY-MM-DD>"
git push
```
אם הפוש נכשל, `git pull --rebase` ואז פוש שוב פעם אחת. **הרוטינה `clart-dashboard-sync` כותבת `data.json` באותו ריפו כמה פעמים ביום, ולכן התנגשות פוש היא נורמלית ומטופלת ב-rebase. אל תיגע ב-`data.json` ואל תיגע ב-`index.html`.**

## בסוף
משפט סיכום קצר: אילו ערוצים הצליחו, אילו נכשלו, מה הדבר הדחוף ביותר, והאם הפוש הצליח.

---

# מדדי בסיס להשוואה, נמדדו 26 עד 28.09.2026

- **תנועה 26.09:** 285 סשנים, המרה אחת בלבד, 188.10 דולר מ-Paid Search. פילוח: Unassigned 76, Direct 65, Paid Other 45, Paid Social 35, Organic Social 28, Email 14, Paid Search 12, Cross-network 5, Organic Search 5.
- **Google Ads 20 עד 26.09:** 105.89 דולר בלבד, 171 חשיפות, 36 קליקים, קליק ב-2.94 דולר. מתוך 13 קמפיינים **רק אחד פעיל**, "Bright | Search | Brand | 27.01.26". אל תדווח 13 כאילו הם רצים.
- **מחזור:** ספטמבר כ-8,962 דולר מעסקאות בלוח ועוד כ-1,264 מהזמנות חנות של מי שאינם לידים. אוגוסט כ-9,910.
- **אחוזי מענה 60 יום:** 211 לידים, 70.7 אחוז מענה ב-30 הימים האחרונים מול 33.7 אחוז בקודמים, אבל הפער הוא בעיקר היגיינת לוח ולא התנהגות לקוחות. **המדד היציב: כ-9 אחוז מהלידים מגיעים לתשלום.**

**שתי בעיות פתוחות שכדאי לבדוק בכל ריצה:**
1. **Unassigned הוא הערוץ הגדול ביותר, כ-27 אחוז מהתנועה.** זו תנועה ש-GA לא הצליח לשייך, בדרך כלל UTM שבור. כל עוד רבע מהתנועה לא משויכת, אי אפשר לדעת מה עובד. אם המספר נשאר גבוה, לרשום כתקלה ולא כנתון.
2. **Organic Search כ-2 אחוז בלבד.** זו נקודת הפתיחה למדוד מולה את סוכנות ה-SEO.

**מלכודות מספרים:**
- `cost_micros` הוא מיליוניות, **לחלק במיליון**. 105,890,000 הם 105.89 דולר.
- **אזור הזמן של נכס ה-GA הוא America/Los_Angeles ולא ישראל.** "אתמול" ב-GA אינו "אתמול" בשופיפיי, פער עד עשר שעות. לבדוק יום לכל כיוון לפני שמכריזים על אי התאמה.
- **Google Ads רץ בשקלים והדשבורד בדולרים.** להמיר בשער קבוע 1 דולר = 3 שקל (`ilsToUsd = 0.333333`), כפי שנקבע ב-28.09.2026 כי `open.er-api.com` חסום לצמיתות בסביבת הריצה. לא לנסות שער חי.
- `conversions_value` ב-Ads לא שווה ל-`purchaseRevenue` ב-GA ולא לשופיפיי. **אין להכריז ROAS על סמך Ads לבדו. שופיפיי הוא מקור האמת להכנסה.**

---

# חלק ב': סנכרון דשבורד שעתי (clart-dashboard-sync בלבד)

אתה מסנכרן כמה פעמים ביום (06:00, 09:00, 12:00, 15:00, 18:00, 21:00, 23:00 שעון ישראל) נתונים חיים לאתר דשבורד שיווקי חיצוני של Albert Levi Art. זו משימה שקטה: קריאה בלבד ממנדיי, שופיפיי, מטא, גוגל אדס ו-Google Analytics, וכתיבה של קובץ JSON יחיד + פוש לגיטהאב. אל תשנה שום דבר באף אחד מהמקורות. אל תשלח שום הודעה לאף אחד, לא רלוונטי כלל.

## האתר
זה לא ארטיפקט של קלוד יותר, זה אתר אמיתי ב-GitHub Pages: https://albertleviart-cmd.github.io/clart-dashboard/
קלון מקומי קבוע נמצא ב: /Users/albertlevi/Documents/ChristianLightArt/art-agent/clart-dashboard
הדף (index.html) קורא כל דקה מ-data.json באותה תיקייה. אתה רק כותב מחדש את data.json ודוחף אותו לגיטהאב, לא נוגע ב-index.html.

**שלב ראשון, תמיד:** `cd` לתיקייה הזו, `git pull` (כדי לא להתנגש עם עצמך אם ריצה קודמת עדיין בתהליך), קרא את data.json הקיים עם Read כדי לדעת מה יש שם עכשיו (בשביל merge חכם אם ערוץ נכשל).

## מקור 1: מנדיי (MCP 6306f720), לוח Leads 2037430545
אם לא קראת get_board_info בסשן הזה, קרא אותו קודם כדי לוודא שמזהי העמודות תקפים.

**משפך לידים (מצב חי עכשיו):**
board_insights: aggregations על lead_status (בלי פונקציה) ועם COUNT, groupBy lead_status.
מפה לשש קבוצות, המפתחות האלה בדיוק באנגלית:
- "new": "New Lead"
- "working": "First Message Sent","Follow Up 1 Sent".."Follow Up 7 Sent","Final Contact Attempt Sent","Conversation Active","In Discussion","Contact Later","Unreachable"
- "negotiating": "Negotiating","Proposal Sent","Waiting for Client"
- "deposit_production": "Deposit Pending","Deposit Paid","In Production","Ready to Ship","Shipped","Delivered"
- "closed": "Closed","Collector 90 Days","Collector 180 Days","Collector 365 Days"
- "lost": "Lost","Not Interested Now","No Budget Now"
- "remarketing_pool": "remarketing"
תווית ריקה או לא ברשימה, תתעלם. בנה מערך funnel עם שבעה אובייקטים {key, label (עברית קצרה), count} בסדר: new, working, negotiating, deposit_production, closed, lost, remarketing_pool.
totalLeads מ-items_count של get_board_info.

**קצב כניסת לידים (leadVelocity):** ארבע קריאות board_insights נפרדות עם aggregations COUNT_ITEMS, filters על העמודה הווירטואלית "__creation_log__" עם operator "within_the_last" ו-compareValue ["DAYS", N]:
- yesterday: N=1 (חלון גלגל של 24 שעות, לא בהכרח יום קלנדרי)
- last7d: N=7
- last14d: N=14
- last30d: N=30

**פילוח מקור הלידים (leadSources):** ארבע קריאות board_insights נוספות, זהות לאלה של leadVelocity אבל עם פילטר שני: העמודה `text_mksv58se` ("why connected?") עם operator "is_not_empty" ו-compareValue מערך ריק `[]`, ב-filtersOperator "and".

זו עמודת שאלה של טופס Powerful Form Builder באתר, ורק הטופס ממלא אותה, לכן היא הסימן האמין לזיהוי ליד שהגיע מהטופס. אל תשתמש בעמודות ה-UTM לצורך הזה: הן שבורות. `utm_medium` לא נכתב יותר בכלל (אפס לידים ב-30 יום), ו-`utm_source` מכילה ערכים פגומים כמו "utm_sourcefacebook" שהם שמות שדות שנדחפו לתוך הערך.

לכל אחת מארבע התקופות כתוב `leadSources.<תקופה>` = {total: המספר מ-leadVelocity לאותה תקופה, form: המספר מהקריאה עם הפילטר, other: total פחות form}. **חובה למדוד את total ו-form באותה ריצה ובסמיכות זמן**, אחרת ליד שנכנס בין שתי הקריאות ייחשב בטעות ל-other.

**חשוב, כדי שתבין למה המספרים האלה לא זזים בין ריצה לריצה:** הלידים החדשים במנדיי עצמם נוצרים בעיקר על ידי הרוטינה היומית הנפרדת clart-leads-sync שרצה פעם ביום ב-07:03 ומכניסה למנדיי לידים חדשים מ-Quo, שני תיבות המייל, ומהאתר. הריצה שלך כאן רק קוראת את המצב הנוכחי של מנדיי, היא לא סורקת Quo או מייל בעצמה. אז leadVelocity ו-funnel ישתנו בעיקר פעם ביום אחרי שהרוטינה הבוקר רצה, ולא בכל ריצה שלך. זה תקין, אל תנסה לסרוק ערוצים אחרים כדי "לתקן" את זה.

**עסקאות שנסגרו לפי תקופה (closedDeals):** לכל אחת מארבע התקופות today/last7d/last30d/monthToDate, board_insights עם aggregations על numeric_mm5f7m9m עם COUNT ו-SUM, filters על **date_mm7kecgd (Close Date)**:
- today: operator "within_the_last", compareValue ["DAYS",1]
- last7d: operator "within_the_last", compareValue ["DAYS",7]
- last30d: operator "within_the_last", compareValue ["DAYS",30]
- monthToDate: operator "greater_than_or_equals", compareValue התאריך של היום הראשון בחודש הנוכחי (YYYY-MM-DD)
כל תוצאה: {revenue: SUM (0 אם ריק), count: COUNT}. זה סופר לפי סטטוס נוכחי, כי מקוריים ומכירות פרטיות (סטרייפ ישיר, טלפון, מייל, וואטסאפ) לא עוברות אף פעם דרך שופיפיי.

**שונה ב-28.09.2026 מ-date_mm5pc346 (Deposit Date) ל-date_mm7kecgd (Close Date), וזה תיקון מהותי.** אלברט מודד מחזור לפי **מועד סגירת העסקה** ולא לפי מתי הכסף נכנס: עסקה על 3,000 דולר עם מקדמה 750 נספרת כ-3,000 בחודש שנסגרה, והשלמת יתרה על עסקה מחודש קודם נספרת כאפס. **Deposit Date היה שבור לצורך הזה משתי סיבות:** הוא היה ריק ברוב הרשומות, כך שכל העסקאות הגדולות מחוץ לאתר (Amy Yaffe, Stephen, Anna Katz, Ron Zakai, Moshe, Henry) לא נספרו בכלל, והוא גם החזיק גם תאריכי תשלום מלא וגם תאריכי מקדמה, שני דברים שונים באותה עמודה. Close Date נכתב פעם אחת בסגירה ואינו זז כשמגיעה השלמת תשלום. **שים לב: עמודת Deal Value מחזיקה גם הצעות מחיר שלא נסגרו, ולכן סינון לפי Close Date הוא מה שמונע מהן להיספר כהכנסה. אל תסכום את Deal Value בלי הסינון הזה.**

אם מנדיי נכשל: sources.monday = {status:"error", note:"<תיאור קצר>"}. אל תכתוב funnel/totalLeads/leadVelocity/leadSources/closedDeals בריצה הזו, השאר את הישן מה-data.json שקראת בהתחלה. אם הצליח: sources.monday = {status:"ok", note:""}.

## מקור 2: שופיפיי (MCP d531e1fe), חנות albertlevi.com

**באג ידוע ותוקן ב-27.09.2026, אל תחזור אליו:** גרסה קודמת השתמשה ב-run-analytics-query עם `FROM sales SHOW net_sales, orders GROUP BY product_title` וסכמה את orders מכל השורות. זה ניפח את מספר ההזמנות (הראה 15 כשבשופיפיי עצמו היו 11 ב-7 ימים), כי הזמנה עם כמה מוצרים שונים נספרה פעם אחת לכל מוצר. **אל תשתמש ב-run-analytics-query בכלל למספר ההזמנות.** השיטה הנכונה היא graphql_query על orders ישירות, כמו שמתואר למטה.

**בוטל ב-28.09.2026, אל תחזור לזה:** גרסה קודמת עשתה כאן קריאת search_products נפרדת עם tag:'original work' כ"בדיקה שנייה" לרשימת המקוריים. זה יותר מ-130 מוצרים ופלט JSON ענק בכל ריצה, בלי שום תועלת בפועל: אלברט לא סוגר ציורים מקוריים דרך שופיפיי בכלל, אלא כלידים בעסקאות פרטיות (נספר תחת monday closedDeals). הסיווג originals/prints למטה ממשיך להתבסס אך ורק על תגית "original work" בפועל על ה-product בתוך שאילתת ה-orders עצמה (product.tags), בלי שום קריאת אימות נוספת. בפועל originals תמיד יצא 0, וזה תקין.

**הכנסות והזמנות לפי תקופה, דרך graphql_query על orders:**
לכל אחת מארבע התקופות (today/-7d/-30d/היום הראשון בחודש עד עכשיו), שאילתה:
```
orders(first: 100, query: "created_at:>='<תאריך התחלה ISO>' AND created_at:<='<תאריך סוף ISO>'") {
  edges {
    node {
      name
      createdAt
      cancelledAt
      subtotalPriceSet { shopMoney { amount } }
      lineItems(first: 50) {
        edges { node { title discountedTotalSet { shopMoney { amount } } product { tags } } }
      }
    }
  }
  pageInfo { hasNextPage endCursor }
}
```
דפדף עם `after: <endCursor>` עד ש-hasNextPage הוא false. אם יש מעל 100 הזמנות בתקופה (בעיקר ל-30 יום ולחודש), זה צפוי, תמשיך לדפדף.

**עיבוד לכל הזמנה שחזרה:**
1. אם cancelledAt לא null, דלג על ההזמנה הזו לגמרי, היא לא נספרת לא בהכנסה ולא במספר ההזמנות.
2. אחרת: site.orders += 1, site.revenue += subtotalPriceSet.shopMoney.amount (כמספר).
3. עבור על lineItems: אם ל-product.tags יש "original work", הוסף את discountedTotalSet.shopMoney.amount ל-originalsLineRevenue של ההזמנה הזו, אחרת ל-printsLineRevenue.
4. אם originalsLineRevenue > 0 להזמנה: originals.revenue += originalsLineRevenue, originals.orders += 1.
5. אם printsLineRevenue > 0 להזמנה: prints.revenue += printsLineRevenue, prints.orders += 1.
   (הזמנה נדירה עם גם מקורי וגם פרינט תיספר בשתי הקטגוריות, זה תקין ושקוף, עדיף מהמרה שקרית לקטגוריה אחת.)

**הערת דיוק:** subtotalPriceSet הוא הסכום לפני משלוח ומס ואחרי הנחות, אבל **לפני** זיכויים חלקיים שניתנו אחרי ההזמנה. הזמנות שבוטלו לגמרי (cancelledAt) כן מוחרגות, אבל זיכוי חלקי לא מופחת. זה מספיק טוב לדשבורד יומיומי, לא מדויק לצרכי הנהלת חשבונות.

periods.<תקופה>.site = {revenue, orders, originals:{revenue,orders}, prints:{revenue,orders}}.

אם שופיפיי נכשל: sources.shopify = {status:"error", note:"<תיאור>"}. אל תכתוב periods.*.site בכלל הריצה הזו, השאר את הישן. אם הצליח: sources.shopify = {status:"ok", note:""}.

## מקור 3: מטא אדס (MCP ff90e049)
חשבון פעיל: "Albert Levi Art", ad_account_id "400319919198934", USD (זהה למטבע שופיפיי).
לכל תקופה, ads_get_ad_entities: ad_account_id "400319919198934", level "ad_account", fields ["amount_spent"], date_preset: today→"today", last7d→"last_7d", last30d→"last_30d", monthToDate→"this_month". חובה client_conversation_id (20 תווים אקראיים, אותו לכל הקריאות בריצה) ו-advertiser_request. התעלם מ-next_actions אם מופיע.
periods.<תקופה>.spendBreakdown.meta = amount_spent (0 אם חסר). זה רק החלק של מטא, ה-spend הכולל מחושב בהמשך.

גם בדוק "2111326855852065" (חשבון "Albert Levi", ILS) עם date_preset "last_30d". אם amount_spent>0, כתוב הערה קצרה ב-metaAccountNote שיש הוצאה נוספת בחשבון הזה שלא נכללת (מטבע שונה). אחרת metaAccountNote ריק או ציון שם החשבון הראשי בלבד.

אם מטא נכשל: sources.metaAds = {status:"error", note:"<תיאור>"}, אל תכתוב spendBreakdown.meta הריצה הזו, השאר את הישן. אם הצליח: sources.metaAds = {status:"ok", note:""}.

## מקור 4: גוגל אדס (Composio, MCP fe5ad87c)
חשבון: "אלברט ארט", customer_id "7282991915", חשבון בודד (לא MCC). **החשבון מתנהל בשקלים** והדשבורד כולו בדולרים, לכן חייבים להמיר. חובר ב-27.09.2026.

הכלי: COMPOSIO_MULTI_EXECUTE_TOOL עם tool_slug "GOOGLEADS_SEARCH_STREAM_GAQL". אפשר לשלוח את כל ארבע השאילתות בקריאה אחת במקביל, הן לא תלויות אחת בשנייה. אם צריך לגלות מחדש את ה-slug או שהחיבור נראה לא פעיל, הרץ קודם COMPOSIO_SEARCH_TOOLS.

לכל תקופה, arguments: {customer_id: "7282991915", query: "..."}:
- today: `SELECT metrics.cost_micros FROM customer WHERE segments.date DURING TODAY`
- last7d: `SELECT metrics.cost_micros FROM customer WHERE segments.date DURING LAST_7_DAYS`
- last30d: `SELECT metrics.cost_micros FROM customer WHERE segments.date DURING LAST_30_DAYS`
- monthToDate: `SELECT metrics.cost_micros FROM customer WHERE segments.date DURING THIS_MONTH`

costMicros חוזר כמחרוזת ובמיקרו-יחידות. חלק ב-1,000,000 כדי לקבל שקלים. תוצאה ריקה (results חסר או ריק) זה לא שגיאה, זה אפס.

**המרה לדולר: שער קבוע, נקבע על ידי אלברט ב-28.09.2026.** `open.er-api.com` חסום לצמיתות על ידי הפרוקסי של סביבת הריצה (403, connect_rejected על מדיניות הארגון), לא תקלה זמנית, ואין טעם לנסות שוב בכל ריצה. **אל תנסה למשוך שער חי יותר.** השער הקבוע: 1 דולר = 3 שקל, כלומר `ilsToUsd = 0.333333`.
periods.<תקופה>.spendBreakdown.google = עלות בשקלים × 0.333333, מעוגל ל-2 ספרות.
כתוב fx = {ilsToUsd: 0.333333, asOf: "fixed", source: "ידני, 1 USD = 3 ILS, נקבע 28.09.2026"}.
אם אלברט יבקש בעתיד לחזור לשער חי או לעדכן את השער הקבוע, זה עריכה של הקובץ הזה, לא של הרוטינה.

אם גוגל נכשל: sources.googleAds = {status:"error", note:"<תיאור קצר>"}, אל תכתוב spendBreakdown.google הריצה הזו, השאר את הישן. אם הצליח: sources.googleAds = {status:"ok", note:""}.

## מקור 5: Google Analytics (Composio, MCP fe5ad87c)
property: "properties/415263029" (Albert art shopify, USD, זה החנות הראשית שהדשבורד עוסק בה, לא properties/440744270 שזה האתר הישראלי הנפרד). חובר ב-27.09.2026.

הכלי: COMPOSIO_MULTI_EXECUTE_TOOL עם tool_slug "GOOGLE_ANALYTICS_RUN_REPORT". שלח את ארבע הבקשות (today/last7d/last30d/monthToDate) בקריאה אחת במקביל.

לכל תקופה, arguments: {property: "properties/415263029", dateRanges: [{startDate: "<תאריך התחלה או 'today'/'7daysAgo'/'30daysAgo'>", endDate: "today"}], metrics: [{name:"sessions"},{name:"transactions"}]}.
עבור monthToDate, startDate הוא התאריך של היום הראשון בחודש הנוכחי (YYYY-MM-DD), לא מחרוזת יחסית.
בלי dimensions, כדי שתחזור שורה אחת עם הסכומים לכל התקופה. אם rows ריק, sessions=0 ו-transactions=0.
metricValues מוחזרים כמחרוזות, המר למספרים.

periods.<תקופה>.analytics = {sessions, transactions}.

אם Google Analytics נכשל: sources.googleAnalytics = {status:"error", note:"<תיאור קצר>"}, אל תכתוב periods.*.analytics הריצה הזו, השאר את הישן. אם הצליח: sources.googleAnalytics = {status:"ok", note:""}.

## בניית data.json
אובייקט מלא: {updatedAt (ISO עם offset ישראל, למשל 2026-09-27T14:32:00+03:00), sources (כולל monday, shopify, metaAds, googleAds, googleAnalytics), metaAccountNote, fx, periods (today/last7d/last30d/monthToDate, כל אחד {site, closedDeals, spendBreakdown:{meta,google}, spend, analytics:{sessions,transactions}}), leadVelocity, leadSources (yesterday/last7d/last14d/last30d, כל אחד {total, form, other}), funnel, totalLeads, history:{days}}.
לשדות שנכשלו הריצה הזו, קח את הערך הישן מה-data.json שקראת בשלב הראשון במקום למחוק אותו.

**הוצאה כוללת:** periods.<תקופה>.spend = spendBreakdown.meta + spendBreakdown.google, הכל בדולרים. זה השדה שהדף משתמש בו ל-ROAS ול-CPA, אז הוא חייב להיות הסכום של השניים ולא רק מטא. אם אחד הערוצים נכשל הריצה הזו, סכום את הערך הישן שלו מה-data.json הקיים כדי ש-spend יישאר שלם.

**היסטוריה:** מתוך history.days הקיים, מצא רשומה עם date של היום (YYYY-MM-DD שעון ישראל). עדכן אותה (או הוסף חדשה) עם siteRevenue=periods.today.site.revenue, closedDealsRevenue=periods.today.closedDeals.revenue, spend=periods.today.spend, roasSite=(spend>0 ? siteRevenue/spend : 0), roasTotal=(spend>0 ? (siteRevenue+closedDealsRevenue)/spend : 0). שמור 30 רשומות אחרונות, מיין עולה לפי תאריך. דלג על עדכון ההיסטוריה אם שופיפיי או מטא נכשלו הריצה הזו (כשל של גוגל אדס או Google Analytics בלבד לא עוצר את ההיסטוריה). שים לב שרשומות היסטוריה מלפני 27.09.2026 מכילות spend של מטא בלבד, כי גוגל עוד לא היה מחובר.

כתוב את הקובץ עם Write ל-data.json (JSON תקין, ללא הערות).

## פרסום
בתיקייה /Users/albertlevi/Documents/ChristianLightArt/art-agent/clart-dashboard:
git add data.json
git commit -m "sync <timestamp>"
git push
אם git push נכשל (למשל התנגשות), נסה git pull --rebase ואז git push שוב פעם אחת. אם עדיין נכשל, ציין את זה במשפט הסיכום, אל תמחוק שום דבר.

## בסוף
אל תכתוב דוח לצ'אט, אל תשמור קובץ נפרד. משפט סיכום אחד בעברית: אילו מקורות הצליחו, אילו נכשלו, והאם ה-push הצליח.