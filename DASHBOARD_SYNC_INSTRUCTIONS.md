# הוראות סנכרון הדשבורד

הקובץ הזה הוא מקור האמת לרוטינת הענן `clart-dashboard-sync`, שרצה כל שעה וכותבת `data.json`.
עדכון ההתנהגות נעשה בעריכת הקובץ הזה ופוש לגיטהאב, לא בעריכת הרוטינה.

**אל תיגע ב-`index.html`, ב-`leads.json` או ב-`reports/`.** אלה של הרוטינה האחרת, `clart-leads-sync`.

---

אתה מסנכרן כל שעה נתונים חיים לאתר דשבורד שיווקי חיצוני של Albert Levi Art. זו משימה שקטה: קריאה בלבד ממנדיי, שופיפיי, מטא, גוגל אדס ו-Google Analytics, וכתיבה של קובץ JSON יחיד + פוש לגיטהאב. אל תשנה שום דבר באף אחד מהמקורות. אל תשלח שום הודעה לאף אחד, לא רלוונטי כלל.

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

**חשוב, כדי שתבין למה המספרים האלה לא זזים כל שעה:** הלידים החדשים במנדיי עצמם נוצרים בעיקר על ידי הרוטינה היומית הנפרדת daily-leads-sync שרצה פעם ביום ב-07:03 ומכניסה למנדיי לידים חדשים מ-Quo, שני תיבות המייל, ומהאתר. הריצה שלך כאן רק קוראת את המצב הנוכחי של מנדיי, היא לא סורקת Quo או מייל בעצמה. אז leadVelocity ו-funnel ישתנו בעיקר פעם ביום אחרי שהרוטינה הבוקר רצה, ולא בכל ריצה שלך. זה תקין, אל תנסה לסרוק ערוצים אחרים כדי "לתקן" את זה.

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

**המרה לדולר:** משוך שער חי:
`curl -s -m 12 "https://open.er-api.com/v6/latest/ILS"` וקח rates.USD (וגם time_last_update_utc).
periods.<תקופה>.spendBreakdown.google = עלות בשקלים × rates.USD, מעוגל ל-2 ספרות.
כתוב fx = {ilsToUsd: <השער>, asOf: "<YYYY-MM-DD של time_last_update_utc>", source: "open.er-api.com"}.
אם משיכת השער נכשלה, השתמש ב-fx.ilsToUsd הישן מה-data.json שקראת בהתחלה והשאר את asOf הישן. אם גם זה לא קיים, אל תכתוב spendBreakdown.google בכלל ורשום את הסיבה ב-sources.googleAds.note. **אל תמציא שער.**

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