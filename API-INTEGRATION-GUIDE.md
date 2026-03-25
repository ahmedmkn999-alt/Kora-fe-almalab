# دليل دمج APIs لموقع كورة في الملعب 🚀

## نظرة عامة
هذا الدليل يشرح كيفية دمج APIs حقيقية لسحب الأخبار الرياضية ونتائج المباريات تلقائياً.

---

## 1️⃣ APIs الرياضية المجانية والمدفوعة

### أ) للأخبار الرياضية:

#### NewsAPI.org (مجاني جزئياً)
```javascript
// 1. سجل في https://newsapi.org
// 2. احصل على API key مجاني
// 3. استخدمه هكذا:

const NEWS_API_KEY = 'YOUR_API_KEY_HERE';
const url = `https://newsapi.org/v2/everything?q=football OR soccer&language=ar&sortBy=publishedAt&apiKey=${NEWS_API_KEY}`;

fetch(url)
  .then(response => response.json())
  .then(data => {
    data.articles.forEach(article => {
      console.log(article.title);
      console.log(article.description);
      console.log(article.urlToImage);
    });
  });
```

**الحد المجاني**: 100 طلب/يوم

---

#### The Guardian API (مجاني)
```javascript
// 1. سجل في https://open-platform.theguardian.com
// 2. احصل على API key

const GUARDIAN_KEY = 'YOUR_KEY';
const url = `https://content.guardianapis.com/search?section=sport&show-fields=thumbnail,body&api-key=${GUARDIAN_KEY}`;

fetch(url)
  .then(res => res.json())
  .then(data => {
    // معالجة الأخبار
  });
```

---

### ب) للمباريات والنتائج:

#### Football-Data.org (مجاني جزئياً)
```javascript
// 1. سجل في https://www.football-data.org
// 2. احصل على API token مجاني

const FOOTBALL_API_TOKEN = 'YOUR_TOKEN';

// جلب المباريات الحية
fetch('https://api.football-data.org/v4/matches', {
  headers: { 'X-Auth-Token': FOOTBALL_API_TOKEN }
})
  .then(res => res.json())
  .then(data => {
    data.matches.forEach(match => {
      console.log(`${match.homeTeam.name} ${match.score.fullTime.home} - ${match.score.fullTime.away} ${match.awayTeam.name}`);
    });
  });

// جلب ترتيب الدوري
fetch('https://api.football-data.org/v4/competitions/PL/standings', {
  headers: { 'X-Auth-Token': FOOTBALL_API_TOKEN }
})
  .then(res => res.json())
  .then(data => {
    // معالجة الترتيب
  });
```

**الحد المجاني**: 10 طلبات/دقيقة

---

#### API-Football (RapidAPI) - قوي جداً
```javascript
// 1. سجل في https://rapidapi.com
// 2. اشترك في API-Football

const options = {
  method: 'GET',
  headers: {
    'X-RapidAPI-Key': 'YOUR_RAPIDAPI_KEY',
    'X-RapidAPI-Host': 'api-football-v1.p.rapidapi.com'
  }
};

// المباريات الحية
fetch('https://api-football-v1.p.rapidapi.com/v3/fixtures?live=all', options)
  .then(res => res.json())
  .then(data => {
    data.response.forEach(match => {
      console.log(match);
    });
  });

// الإحصائيات
fetch('https://api-football-v1.p.rapidapi.com/v3/teams/statistics?league=39&season=2024', options)
  .then(res => res.json())
  .then(data => console.log(data));
```

**السعر**: يبدأ من مجاني (100 طلب/يوم) حتى $99/شهر

---

#### SportsDB API (مجاني تماماً)
```javascript
// لا يحتاج API key!

// جلب معلومات فريق
fetch('https://www.thesportsdb.com/api/v1/json/3/searchteams.php?t=Liverpool')
  .then(res => res.json())
  .then(data => console.log(data));

// جلب المباريات القادمة
fetch('https://www.thesportsdb.com/api/v1/json/3/eventsnextleague.php?id=4328')
  .then(res => res.json())
  .then(data => console.log(data));
```

---

## 2️⃣ RSS Feeds (بديل مجاني)

```javascript
// استخدم خدمة RSS to JSON مثل rss2json.com

const RSS_URL = 'https://api.rss2json.com/v1/api.json?rss_url=';

// مثال: أخبار BBC Sport
fetch(RSS_URL + encodeURIComponent('http://feeds.bbci.co.uk/sport/football/rss.xml'))
  .then(res => res.json())
  .then(data => {
    data.items.forEach(item => {
      console.log(item.title);
      console.log(item.description);
      console.log(item.link);
    });
  });

// مصادر RSS عربية:
// - يلا كورة: https://www.yallakora.com/rss
// - فيلجول: http://www.filgoal.com/rss
// - كورة 365: https://www.kooora365.com/feed
```

---

## 3️⃣ كود JavaScript الكامل للدمج

```javascript
class SportsDataManager {
  constructor() {
    this.newsApiKey = 'YOUR_NEWS_API_KEY';
    this.footballApiKey = 'YOUR_FOOTBALL_API_KEY';
    this.cache = {
      news: [],
      matches: [],
      lastUpdate: null
    };
  }

  // جلب الأخبار
  async fetchNews() {
    try {
      const response = await fetch(
        `https://newsapi.org/v2/everything?q=football+soccer&language=ar&sortBy=publishedAt&apiKey=${this.newsApiKey}`
      );
      const data = await response.json();
      
      this.cache.news = data.articles.map(article => ({
        title: article.title,
        description: article.description,
        image: article.urlToImage,
        url: article.url,
        source: article.source.name,
        publishedAt: article.publishedAt
      }));
      
      return this.cache.news;
    } catch (error) {
      console.error('Error fetching news:', error);
      return this.getSampleNews();
    }
  }

  // جلب المباريات
  async fetchMatches() {
    try {
      const response = await fetch(
        'https://api.football-data.org/v4/matches',
        { headers: { 'X-Auth-Token': this.footballApiKey } }
      );
      const data = await response.json();
      
      this.cache.matches = data.matches.map(match => ({
        homeTeam: match.homeTeam.name,
        awayTeam: match.awayTeam.name,
        homeScore: match.score.fullTime.home || 0,
        awayScore: match.score.fullTime.away || 0,
        status: match.status,
        competition: match.competition.name,
        date: match.utcDate
      }));
      
      return this.cache.matches;
    } catch (error) {
      console.error('Error fetching matches:', error);
      return this.getSampleMatches();
    }
  }

  // بيانات تجريبية كbakcup
  getSampleNews() {
    return [
      {
        title: 'محمد صلاح يسجل هدفين في فوز ليفربول',
        description: 'تألق النجم المصري...',
        image: 'https://via.placeholder.com/600x400',
        source: 'BBC Sport'
      }
    ];
  }

  getSampleMatches() {
    return [
      {
        homeTeam: 'ليفربول',
        awayTeam: 'مانشستر سيتي',
        homeScore: 2,
        awayScore: 1,
        status: 'LIVE'
      }
    ];
  }

  // تحديث تلقائي كل 5 دقائق
  startAutoRefresh() {
    this.fetchNews();
    this.fetchMatches();
    
    setInterval(() => {
      this.fetchNews();
      this.fetchMatches();
    }, 300000); // 5 دقائق
  }
}

// استخدام
const sportsData = new SportsDataManager();
sportsData.startAutoRefresh();
```

---

## 4️⃣ نصائح للتطوير

### ✅ استخدم LocalStorage للتخزين المؤقت
```javascript
// حفظ البيانات
localStorage.setItem('news', JSON.stringify(newsData));

// قراءة البيانات
const cachedNews = JSON.parse(localStorage.getItem('news') || '[]');
```

### ✅ إضافة Loading States
```javascript
function showLoader() {
  document.getElementById('newsGrid').innerHTML = '<div class="loader"></div>';
}

function hideLoader() {
  // إزالة اللودر بعد تحميل البيانات
}
```

### ✅ معالجة الأخطاء
```javascript
try {
  const data = await fetchNews();
  displayNews(data);
} catch (error) {
  displayError('فشل تحميل الأخبار. جاري المحاولة مرة أخرى...');
  // محاولة مرة أخرى بعد 10 ثواني
  setTimeout(() => fetchNews(), 10000);
}
```

---

## 5️⃣ APIs مدفوعة احترافية (للمشاريع الكبيرة)

1. **Sportradar** - الأقوى والأشمل
   - سعر: يبدأ من $500/شهر
   - مميزات: بيانات حية، إحصائيات، فيديوهات

2. **Opta Sports** - بيانات احترافية
   - سعر: حسب الطلب
   - مميزات: إحصائيات دقيقة جداً

3. **Sportmonks** - متوسط السعر
   - سعر: من $50/شهر
   - مميزات: تغطية شاملة للدوريات

---

## 6️⃣ الخطوات التالية

1. ✅ سجل في NewsAPI و Football-Data
2. ✅ احصل على API keys مجانية
3. ✅ استبدل المتغيرات في الكود
4. ✅ اختبر البيانات
5. ✅ أضف المزيد من المميزات

---

## 📞 ملاحظات مهمة

- **الأمان**: لا تعرض API keys في الكود Frontend! استخدم Backend (Node.js/PHP)
- **Rate Limits**: احترم حدود الاستخدام لكل API
- **Caching**: خزن البيانات مؤقتاً لتقليل الطلبات
- **Fallback**: دائماً احتفظ ببيانات احتياطية

---

## 🎯 مثال: Backend بسيط (Node.js)

```javascript
// server.js
const express = require('express');
const axios = require('axios');
const app = express();

app.get('/api/news', async (req, res) => {
  try {
    const response = await axios.get(
      `https://newsapi.org/v2/everything?q=football&apiKey=${process.env.NEWS_API_KEY}`
    );
    res.json(response.data);
  } catch (error) {
    res.status(500).json({ error: 'Failed to fetch news' });
  }
});

app.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

---

**بالتوفيق! 🎉**