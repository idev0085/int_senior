# Google Analytics for React - Complete Guide

## Table of Contents
1. [Google Analytics Fundamentals](#google-analytics-fundamentals)
2. [GA4 vs UA](#ga4-vs-ua)
3. [React Integration](#react-integration)
4. [Implementation Guide](#implementation-guide)
5. [Event Tracking](#event-tracking)
6. [User Properties](#user-properties)
7. [Advanced Features](#advanced-features)
8. [Performance Optimization](#performance-optimization)
9. [Interview Q&A](#interview-qa)

---

## Google Analytics Fundamentals

### What is Google Analytics?
Google Analytics is a web analytics service that tracks and reports website traffic, user behavior, conversion metrics, and audience demographics. GA4 (Google Analytics 4) is the latest version with enhanced event tracking.

### Why Use Google Analytics?
- **User Insights**: Understand who visits your site and their behavior
- **Traffic Analysis**: See where traffic comes from
- **Conversion Tracking**: Measure goals and business outcomes
- **Audience Understanding**: Demographics, interests, devices
- **Real-time Monitoring**: See live visitor activity
- **A/B Testing**: Experiment with changes and measure impact
- **Integration**: Connect with Google Ads, Search Console, BigQuery

### Key Benefits
- Free tier with substantial data processing
- Real-time reporting
- Powerful audience segmentation
- Integration with other Google products
- Custom events and properties
- Advanced analysis and reporting

---

## GA4 vs UA

### Universal Analytics (UA) - Legacy
- Session-based tracking
- Limited event tracking
- Separate mobile/web tracking
- Being phased out (ended July 2023)

### Google Analytics 4 (GA4) - Current
- Event-based tracking model
- Enhanced measurement
- Unified web and app tracking
- Machine learning insights
- Better privacy compliance
- Recommended for new properties

### Key Differences

| Feature | UA | GA4 |
|---------|----|----|
| Tracking Model | Sessions | Events |
| Mobile/Web | Separate | Unified |
| Real-time Data | Limited | Excellent |
| Privacy | Basic | Enhanced |
| Custom Events | Limited | Extensive |
| AI Insights | No | Yes |
| Event Parameters | Limited | Unlimited |
| Cross-device Tracking | Limited | Better |

---

## React Integration

### Setting Up Google Analytics in React

#### Step 1: Install React Google Analytics
```bash
npm install react-ga4
# or
npm install @react-ga/compat
# or use gtag directly
npm install gtag
```

#### Step 2: Initialize in Your App
```javascript
// App.js or main.js
import ReactGA from "react-ga4";

// Initialize Google Analytics
ReactGA.initialize("GA_MEASUREMENT_ID");

// Or using gtag directly
import { initializeApp } from "firebase/app";
import { getAnalytics } from "firebase/analytics";

const app = initializeApp(firebaseConfig);
const analytics = getAnalytics(app);
```

#### Step 3: Add to HTML (Alternative)
```html
<!-- Add to public/index.html in head -->
<script async src="https://www.googletagmanager.com/gtag/js?id=GA_MEASUREMENT_ID"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'GA_MEASUREMENT_ID');
</script>
```

### Basic React GA4 Setup
```javascript
// src/utils/analytics.js
import ReactGA from "react-ga4";

export const initAnalytics = () => {
  ReactGA.initialize(process.env.REACT_APP_GA_ID);
};

export const trackPageView = (path) => {
  ReactGA.send({ 
    hitType: "pageview", 
    page: path 
  });
};

export const trackEvent = (category, action, label, value) => {
  ReactGA.event({
    category: category,
    action: action,
    label: label,
    value: value
  });
};

export const setUserId = (userId) => {
  ReactGA.set({ 'user_id': userId });
};

export const setUserProperties = (properties) => {
  ReactGA.event({
    category: 'user_properties',
    action: 'set_properties',
    ...properties
  });
};
```

### Initialize in React App
```javascript
// App.js
import { useEffect } from 'react';
import { useLocation } from 'react-router-dom';
import { initAnalytics, trackPageView } from './utils/analytics';

function App() {
  const location = useLocation();

  useEffect(() => {
    // Initialize on app load
    initAnalytics();
  }, []);

  useEffect(() => {
    // Track page views on route change
    trackPageView(location.pathname);
  }, [location]);

  return (
    // Your app content
  );
}

export default App;
```

---

## Implementation Guide

### Step 1: Create Google Analytics Account
1. Go to [Google Analytics](https://analytics.google.com/)
2. Sign in with Google account
3. Create new property for your website
4. Get Measurement ID (G-XXXXXXXXXX)
5. Add to environment variables

### Step 2: Install Dependencies
```bash
npm install react-ga4
# or
npm install @react-ga/compat
```

### Step 3: Create Analytics Module
```javascript
// src/services/analytics.js
import ReactGA from "react-ga4";

class Analytics {
  constructor() {
    this.initialized = false;
  }

  init(measurementId) {
    if (!this.initialized && process.env.NODE_ENV === 'production') {
      ReactGA.initialize(measurementId);
      this.initialized = true;
    }
  }

  pageView(path, title) {
    ReactGA.send({
      hitType: "pageview",
      page: path,
      title: title
    });
  }

  event(eventName, eventParams) {
    ReactGA.event({
      action: eventName,
      params: eventParams
    });
  }

  ecommerce(items) {
    ReactGA.event({
      action: 'purchase',
      params: {
        items: items,
        value: items.reduce((sum, item) => sum + item.price, 0),
        currency: 'USD'
      }
    });
  }

  setUser(userId, userData) {
    ReactGA.set({ 
      'user_id': userId,
      'user_properties': userData
    });
  }

  setUserProperty(propertyName, propertyValue) {
    ReactGA.set({
      [propertyName]: propertyValue
    });
  }

  trackException(description, fatal = false) {
    ReactGA.event({
      action: 'exception',
      params: {
        description: description,
        fatal: fatal
      }
    });
  }
}

export default new Analytics();
```

### Step 4: Create Custom Hook
```javascript
// src/hooks/useAnalytics.js
import { useEffect } from 'react';
import { useLocation } from 'react-router-dom';
import analytics from '../services/analytics';

export const useAnalytics = () => {
  const location = useLocation();

  useEffect(() => {
    // Initialize GA4
    analytics.init(process.env.REACT_APP_GA_ID);
  }, []);

  useEffect(() => {
    // Track page view on route change
    analytics.pageView(location.pathname, document.title);
  }, [location.pathname]);

  return analytics;
};

export const useTrackEvent = () => {
  return (eventName, eventData) => {
    analytics.event(eventName, eventData);
  };
};

export const useTrackUser = () => {
  return (userId, userData) => {
    analytics.setUser(userId, userData);
  };
};
```

### Step 5: Use in Components
```javascript
// src/components/LoginForm.js
import { useTrackEvent, useTrackUser } from '../hooks/useAnalytics';

function LoginForm() {
  const trackEvent = useTrackEvent();
  const trackUser = useTrackUser();

  const handleLogin = async (email, password) => {
    try {
      // Login logic
      const user = await login(email, password);

      // Track login event
      trackEvent('login', { 
        method: 'email',
        user_id: user.id 
      });

      // Set user properties
      trackUser(user.id, {
        email: user.email,
        subscription: user.subscription_type,
        signup_date: user.created_at
      });
    } catch (error) {
      trackEvent('login_error', { error: error.message });
    }
  };

  return (
    // Form JSX
  );
}

export default LoginForm;
```

---

## Event Tracking

### Recommended Events

#### 1. Page/Screen Views
```javascript
analytics.pageView('/products', 'Products Page');
```

#### 2. User Actions
```javascript
// Button clicks
trackEvent('button_click', { 
  button_name: 'add_to_cart',
  product_id: '12345'
});

// Form submissions
trackEvent('form_submit', { 
  form_name: 'contact',
  form_location: 'footer'
});

// File downloads
trackEvent('file_download', { 
  file_name: 'whitepaper.pdf',
  file_type: 'pdf'
});
```

#### 3. E-commerce Events
```javascript
// View item
trackEvent('view_item', {
  items: [{
    item_id: '12345',
    item_name: 'Product Name',
    price: 29.99,
    item_category: 'Electronics'
  }]
});

// Add to cart
trackEvent('add_to_cart', {
  items: [{
    item_id: '12345',
    item_name: 'Product Name',
    quantity: 1,
    price: 29.99
  }],
  value: 29.99,
  currency: 'USD'
});

// Purchase
trackEvent('purchase', {
  transaction_id: 'TRX123456',
  value: 99.99,
  currency: 'USD',
  tax: 8.00,
  shipping: 5.00,
  items: [
    {
      item_id: '12345',
      item_name: 'Product 1',
      quantity: 1,
      price: 49.99,
      item_category: 'Electronics'
    },
    {
      item_id: '67890',
      item_name: 'Product 2',
      quantity: 1,
      price: 49.99,
      item_category: 'Electronics'
    }
  ]
});
```

#### 4. Engagement Events
```javascript
// Video engagement
trackEvent('video_start', {
  video_title: 'Product Demo',
  video_duration: 300,
  video_provider: 'youtube'
});

trackEvent('video_progress', {
  video_title: 'Product Demo',
  progress_value: 50
});

// Scroll tracking
trackEvent('scroll', {
  percent_scrolled: 75
});

// Time spent
trackEvent('time_spent', {
  page: '/products',
  time_seconds: 120
});
```

#### 5. Social Sharing
```javascript
trackEvent('share', {
  method: 'facebook',
  content_type: 'product',
  content_id: '12345'
});
```

#### 6. Search
```javascript
trackEvent('search', {
  search_term: 'leather shoes',
  results_count: 42
});
```

#### 7. Lead Generation
```javascript
trackEvent('generate_lead', {
  value: 100,
  currency: 'USD',
  lead_type: 'newsletter'
});
```

#### 8. Exception Tracking
```javascript
trackEvent('exception', {
  description: 'Database connection failed',
  fatal: false
});
```

### Custom Events
```javascript
// Define custom events specific to your app
trackEvent('feature_usage', {
  feature_name: 'dark_mode',
  feature_enabled: true
});

trackEvent('premium_upgrade', {
  user_id: 'user123',
  from_plan: 'free',
  to_plan: 'premium',
  upgrade_date: new Date().toISOString()
});

trackEvent('content_view', {
  content_type: 'blog_post',
  content_id: 'post123',
  author: 'John Doe',
  category: 'Technology'
});
```

---

## User Properties

### Setting User Properties
```javascript
// src/services/analytics.js
export const setUserProperties = (userId, userProps) => {
  analytics.setUser(userId, {
    email: userProps.email,
    subscription_type: userProps.subscriptionType,
    account_type: userProps.accountType,
    signup_date: userProps.signupDate,
    lifetime_value: userProps.ltv,
    country: userProps.country,
    language: userProps.language
  });
};
```

### Custom User Properties
```javascript
// Set after user login
import { getAnalytics, setUserId } from "firebase/analytics";

const analytics = getAnalytics();
setUserId(analytics, userId);

// With react-ga4
ReactGA.set({ 
  'user_id': userId,
  'user_type': 'premium',
  'signup_source': 'referral',
  'mrr': 99
});
```

### User Segmentation
```javascript
// Create segments based on properties
const userSegments = {
  isPremium: user.subscription === 'premium',
  isNewUser: daysSinceSignup < 7,
  isActiveUser: lastActivityDate > 7,
  highValue: ltv > 1000
};

Object.entries(userSegments).forEach(([key, value]) => {
  analytics.setUserProperty(key, value);
});
```

---

## Advanced Features

### 1. E-commerce Tracking
```javascript
// src/hooks/useEcommerce.js
import { useTrackEvent } from './useAnalytics';

export const useEcommerce = () => {
  const trackEvent = useTrackEvent();

  const trackProduct = (product) => {
    trackEvent('view_item', {
      items: [{
        item_id: product.id,
        item_name: product.name,
        item_category: product.category,
        item_brand: product.brand,
        price: product.price,
        currency: 'USD'
      }]
    });
  };

  const trackAddToCart = (item, quantity) => {
    trackEvent('add_to_cart', {
      value: item.price * quantity,
      currency: 'USD',
      items: [{
        item_id: item.id,
        item_name: item.name,
        quantity: quantity,
        price: item.price,
        item_category: item.category
      }]
    });
  };

  const trackCheckout = (items, value) => {
    trackEvent('begin_checkout', {
      items: items,
      value: value,
      currency: 'USD'
    });
  };

  const trackPurchase = (transaction) => {
    trackEvent('purchase', {
      transaction_id: transaction.id,
      value: transaction.total,
      currency: 'USD',
      tax: transaction.tax,
      shipping: transaction.shipping,
      affiliation: transaction.storeName,
      items: transaction.items.map(item => ({
        item_id: item.id,
        item_name: item.name,
        item_category: item.category,
        price: item.price,
        quantity: item.quantity
      }))
    });
  };

  return { trackProduct, trackAddToCart, trackCheckout, trackPurchase };
};
```

### 2. Content Tracking
```javascript
// src/hooks/useContentTracking.js
export const useContentTracking = () => {
  const trackEvent = useTrackEvent();

  const trackContentView = (content) => {
    trackEvent('view_content', {
      content_type: content.type, // 'blog', 'video', 'article'
      content_id: content.id,
      content_title: content.title,
      content_category: content.category,
      author: content.author,
      published_date: content.publishedDate
    });
  };

  const trackShare = (content, platform) => {
    trackEvent('share', {
      method: platform,
      content_type: content.type,
      content_id: content.id,
      content_title: content.title
    });
  };

  const trackComment = (content) => {
    trackEvent('comment', {
      content_type: content.type,
      content_id: content.id
    });
  };

  return { trackContentView, trackShare, trackComment };
};
```

### 3. Form Analytics
```javascript
// src/hooks/useFormAnalytics.js
export const useFormAnalytics = (formName) => {
  const trackEvent = useTrackEvent();

  const trackFormStart = () => {
    trackEvent('form_start', {
      form_name: formName
    });
  };

  const trackFormSubmit = (data) => {
    trackEvent('form_submit', {
      form_name: formName,
      form_data: Object.keys(data)
    });
  };

  const trackFormError = (fieldName, errorMessage) => {
    trackEvent('form_error', {
      form_name: formName,
      field_name: fieldName,
      error_message: errorMessage
    });
  };

  const trackFormAbandon = (lastFilledField) => {
    trackEvent('form_abandon', {
      form_name: formName,
      last_field: lastFilledField
    });
  };

  return { trackFormStart, trackFormSubmit, trackFormError, trackFormAbandon };
};
```

### 4. Scroll Tracking
```javascript
// src/hooks/useScrollTracking.js
import { useEffect } from 'react';
import { useTrackEvent } from './useAnalytics';

export const useScrollTracking = () => {
  const trackEvent = useTrackEvent();
  const scrollThresholds = [25, 50, 75, 100];
  const trackedScrolls = new Set();

  useEffect(() => {
    const handleScroll = () => {
      const windowHeight = window.innerHeight;
      const docHeight = document.documentElement.scrollHeight;
      const scrollTop = window.scrollY;
      
      const percentScrolled = Math.round(
        (scrollTop + windowHeight) / docHeight * 100
      );

      scrollThresholds.forEach(threshold => {
        if (
          percentScrolled >= threshold && 
          !trackedScrolls.has(threshold)
        ) {
          trackedScrolls.add(threshold);
          trackEvent('scroll', {
            percent_scrolled: threshold
          });
        }
      });
    };

    window.addEventListener('scroll', handleScroll);
    return () => window.removeEventListener('scroll', handleScroll);
  }, []);
};
```

### 5. Performance Tracking
```javascript
// src/hooks/usePerformanceTracking.js
import { useEffect } from 'react';
import { useTrackEvent } from './useAnalytics';

export const usePerformanceTracking = () => {
  const trackEvent = useTrackEvent();

  useEffect(() => {
    // Wait for page to load
    window.addEventListener('load', () => {
      // Get performance data
      const perfData = window.performance.timing;
      const pageLoadTime = 
        perfData.loadEventEnd - perfData.navigationStart;
      const connectTime = 
        perfData.responseEnd - perfData.requestStart;
      const renderTime = 
        perfData.domContentLoaded - perfData.navigationStart;

      trackEvent('page_load_timing', {
        page_load_time: pageLoadTime,
        connect_time: connectTime,
        render_time: renderTime,
        dns_time: perfData.domainLookupEnd - perfData.domainLookupStart
      });
    });
  }, []);
};
```

### 6. Web Vitals Tracking
```javascript
// src/hooks/useWebVitals.js
import { useEffect } from 'react';
import { getCLS, getFID, getFCP, getLCP, getTTFB } from 'web-vitals';
import { useTrackEvent } from './useAnalytics';

export const useWebVitals = () => {
  const trackEvent = useTrackEvent();

  useEffect(() => {
    // Largest Contentful Paint
    getLCP((metric) => {
      trackEvent('web_vital_lcp', {
        value: Math.round(metric.value),
        rating: metric.rating
      });
    });

    // First Input Delay
    getFID((metric) => {
      trackEvent('web_vital_fid', {
        value: Math.round(metric.value),
        rating: metric.rating
      });
    });

    // Cumulative Layout Shift
    getCLS((metric) => {
      trackEvent('web_vital_cls', {
        value: metric.value.toFixed(3),
        rating: metric.rating
      });
    });

    // First Contentful Paint
    getFCP((metric) => {
      trackEvent('web_vital_fcp', {
        value: Math.round(metric.value),
        rating: metric.rating
      });
    });

    // Time to First Byte
    getTTFB((metric) => {
      trackEvent('web_vital_ttfb', {
        value: Math.round(metric.value),
        rating: metric.rating
      });
    });
  }, []);
};
```

### 7. Error Tracking
```javascript
// src/hooks/useErrorTracking.js
import { useEffect } from 'react';
import { useTrackEvent } from './useAnalytics';

export const useErrorTracking = () => {
  const trackEvent = useTrackEvent();

  useEffect(() => {
    // JavaScript errors
    window.addEventListener('error', (event) => {
      trackEvent('js_error', {
        message: event.message,
        source: event.filename,
        line: event.lineno,
        column: event.colno,
        stack: event.error?.stack
      });
    });

    // Unhandled promise rejections
    window.addEventListener('unhandledrejection', (event) => {
      trackEvent('unhandled_rejection', {
        reason: event.reason?.message || event.reason,
        promise: event.promise
      });
    });
  }, []);
};
```

---

## Performance Optimization

### 1. Lazy Load Google Analytics
```javascript
// src/utils/lazyLoadGA.js
let gaLoaded = false;

export const lazyLoadGA = async (measurementId) => {
  if (gaLoaded) return;
  
  return new Promise((resolve) => {
    // Delay GA initialization
    setTimeout(() => {
      import('react-ga4').then((ReactGA) => {
        ReactGA.initialize(measurementId);
        gaLoaded = true;
        resolve();
      });
    }, 2000); // Load after 2 seconds
  });
};
```

### 2. Batch Events
```javascript
// src/services/eventQueue.js
class EventQueue {
  constructor(batchSize = 10, flushInterval = 5000) {
    this.queue = [];
    this.batchSize = batchSize;
    this.flushInterval = flushInterval;
    this.timer = setInterval(() => this.flush(), flushInterval);
  }

  add(event) {
    this.queue.push(event);
    if (this.queue.length >= this.batchSize) {
      this.flush();
    }
  }

  flush() {
    if (this.queue.length === 0) return;
    
    // Send events to GA
    this.queue.forEach(event => {
      ReactGA.event(event);
    });
    this.queue = [];
  }

  destroy() {
    clearInterval(this.timer);
    this.flush();
  }
}

export default new EventQueue();
```

### 3. Debounce Tracking
```javascript
// src/utils/debounceTrack.js
export const createDebouncedTrack = (trackFunction, delay = 1000) => {
  let timeoutId;
  
  return (...args) => {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => {
      trackFunction(...args);
    }, delay);
  };
};

// Usage
const debouncedScrollTrack = createDebouncedTrack(
  (percent) => trackEvent('scroll', { percent_scrolled: percent }),
  500
);
```

### 4. Conditional Tracking
```javascript
// Only track in production
const shouldTrack = () => process.env.NODE_ENV === 'production';

if (shouldTrack()) {
  analytics.init(process.env.REACT_APP_GA_ID);
}

// Only track for users not in testing
const isTestUser = (userId) => userId.includes('test');

if (!isTestUser(userId)) {
  trackEvent('user_action', { userId });
}
```

---

## Interview Q&A

### 1. What is Google Analytics 4?
**Answer**: GA4 is Google's next-generation analytics platform featuring event-based tracking (instead of session-based), unified web and app tracking, enhanced measurement, machine learning insights, and better privacy compliance.

### 2. What is the difference between GA4 and Universal Analytics?
**Answer**: UA was session-based with separate mobile/web tracking. GA4 is event-based with unified tracking, better real-time data, more extensive custom events, and machine learning capabilities. UA was sunset in July 2023.

### 3. How do you implement Google Analytics in a React application?
**Answer**: Install react-ga4 package, initialize with measurement ID, create utility functions for tracking, and use custom hooks to track page views and events throughout the application.

### 4. What is a measurement ID in Google Analytics?
**Answer**: A unique identifier (G-XXXXXXXXXX) assigned to each Google Analytics property, used to identify where data should be sent in Google Analytics.

### 5. How do you track page views in a React SPA?
**Answer**: Use the useEffect hook with useLocation from react-router-dom to detect route changes and call trackPageView for each new route.

### 6. What are events in GA4?
**Answer**: Events are user interactions or activities (clicks, form submissions, purchases). GA4 allows unlimited custom events with parameters for detailed tracking.

### 7. How do you track e-commerce transactions?
**Answer**: Use the purchase event with item details including item_id, item_name, price, quantity, and category. Include transaction_id, total value, tax, and shipping.

### 8. What are user properties in Google Analytics?
**Answer**: User properties are attributes assigned to individual users (email, subscription type, signup date, etc.) that allow segmentation and personalization.

### 9. How do you identify users in Google Analytics?
**Answer**: Use setUser() or set user_id to track specific users across sessions. Requires user consent and privacy compliance.

### 10. What is the difference between events and user properties?
**Answer**: Events are specific actions/interactions, user properties are persistent attributes. Events are action-focused, user properties are characteristic-focused.

### 11. How do you track form submissions?
**Answer**:
```javascript
trackEvent('form_submit', { 
  form_name: 'contact',
  form_location: 'footer'
});
```

### 12. What is enhanced measurement in GA4?
**Answer**: Automatically tracks certain interactions like page_view, click, scroll, form_submit, file_download without manual implementation.

### 13. How do you track scroll depth?
**Answer**: Use scroll event listener to detect scroll percentage and track at thresholds (25%, 50%, 75%, 100%) using trackEvent.

### 14. What are custom events?
**Answer**: Events specific to your application's needs beyond standard GA4 events. Defined by you based on business requirements.

### 15. How do you track video engagement?
**Answer**: Track video_start, video_progress, video_complete events with video metadata (title, duration, provider).

### 16. What is content grouping?
**Answer**: Way to group pages/screens into logical categories for better analysis. Set via event parameters.

### 17. How do you implement conversion tracking?
**Answer**: Define key events as conversions in GA4 (purchase, signup, lead) and track them as regular events. Mark them as conversions in settings.

### 18. How do you track user demographics?
**Answer**: GA4 uses Google Signals to infer demographics from users signed into Google accounts. Can also set custom user properties.

### 19. What is audience segmentation?
**Answer**: Creating subsets of users based on shared characteristics (behavior, properties, events) for targeted analysis.

### 20. How do you track custom metrics?
**Answer**: Send events with custom parameters containing metric data, then use those parameters in reports and analysis.

### 21. What is the difference between page_view and screen_view?
**Answer**: page_view is for web properties, screen_view is for app properties. In React SPA, use page_view with page path.

### 22. How do you exclude internal traffic from GA4?
**Answer**: Create a filter excluding IP addresses of your office/team to exclude internal traffic from analytics.

### 23. What are sessions in GA4?
**Answer**: GA4 still uses sessions but focuses on events. A session is a period of user activity. Defined by 30-minute inactivity timeout.

### 24. How do you implement user consent for tracking?
**Answer**: Obtain user consent before initializing GA4, store consent preference, and only track if user has consented.

### 25. What is a data stream?
**Answer**: Configuration for collecting data from a specific source (website or app). Each property can have multiple data streams.

### 26. How do you track search functionality?
**Answer**:
```javascript
trackEvent('search', { 
  search_term: 'leather shoes',
  search_category: 'products',
  results_count: 42
});
```

### 27. What is real-time reporting in GA4?
**Answer**: Live view of user activity as it happens, showing current users, active pages, and recent events in real-time.

### 28. How do you track link clicks?
**Answer**: Add click event listeners and track with trackEvent including link text, URL, and link type.

### 29. What are events with custom parameters?
**Answer**: Events enriched with additional key-value parameters providing context. Example: purchase event with transaction_id, value, currency.

### 30. How do you implement A/B testing tracking?
**Answer**: Create experiment variant user properties and track events per variant, comparing results.

### 31. What is funnel analysis?
**Answer**: Analyzing user progression through predefined steps (signup → purchase) to identify where users drop off.

### 32. How do you track plugin/extension usage?
**Answer**: Track plugin initialization, feature usage, errors as custom events with plugin metadata.

### 33. What are remarketing audiences?
**Answer**: Segments of users targeted with ads based on past behavior and engagement with your site.

### 34. How do you implement event-based goals?
**Answer**: Define key events as goals/conversions in GA4 to track completion of important actions.

### 35. What is the difference between document-based and event-based implementation?
**Answer**: Document-based (UA) tracks page views, event-based (GA4) tracks granular user actions.

### 36. How do you track time on page?
**Answer**: Track page_view event on entry and use engagement events to measure active time. GA4 automatically calculates engagement metrics.

### 37. What is cohort analysis?
**Answer**: Grouping users by signup date or behavior to analyze how different groups perform over time.

### 38. How do you implement event deduplication?
**Answer**: Use client-side logic to track events only once, or add deduplication token to prevent duplicate processing.

### 39. What are GTM containers?
**Answer**: Google Tag Manager containers store and manage tags. Alternative to direct GA implementation for more flexibility.

### 40. How do you track external link clicks?
**Answer**:
```javascript
const handleExternalLinkClick = (url) => {
  trackEvent('click', { 
    link_type: 'external',
    link_url: url
  });
};
```

### 41. What is the purpose of user ID feature?
**Answer**: Cross-device and cross-session user tracking. Requires authenticated users and specific setup in GA4.

### 42. How do you track file downloads?
**Answer**: GA4 automatically tracks file downloads as file_download event, or manually track with:
```javascript
trackEvent('file_download', { 
  file_name: 'document.pdf',
  file_type: 'pdf'
});
```

### 43. What is debugging mode in GA4?
**Answer**: Real-time debugging view showing exactly what data is being sent to GA4 for validation.

### 44. How do you implement consent mode?
**Answer**: Notify users of tracking, obtain consent, set consent parameters before GA initialization.

### 45. What are data-driven attribution models?
**Answer**: GA4 uses ML to assign credit to touchpoints in customer journey based on actual conversion data.

### 46. How do you track multi-step processes?
**Answer**: Track each step as separate event and correlate using session_id or user_id to analyze flow.

### 47. What is predictive analytics in GA4?
**Answer**: Machine learning predictions like churn probability, purchase likelihood based on user behavior.

### 48. How do you implement Google Ads integration?
**Answer**: Link Google Ads account to GA4 property to track conversions and create audiences for remarketing.

### 49. What are DoubleClick integration benefits?
**Answer**: Connects GA4 with DoubleClick Campaign Manager for unified campaign tracking and attribution.

### 50. How do you track custom dimensions?
**Answer**: Set event parameters as custom dimensions in GA4, or use custom user properties for user-level dimensions.

### 51. What is the difference between view filters and data filters?
**Answer**: View filters (UA) modified data shown in reports. Data filters (GA4) modify data before it's processed.

### 52. How do you implement cross-domain tracking?
**Answer**: Use gtag with proper configuration and allow list setup to track users across multiple domains.

### 53. What are calculated events in GA4?
**Answer**: Events created from existing events using conditions and logic in GA4 settings.

### 54. How do you track API calls from React?
**Answer**: Wrap API calls and track success/error as events with endpoint and status information.

### 55. What is the difference between impressions and interactions?
**Answer**: Impressions are views, interactions are actions. GA4 focuses on events (interactions).

### 56. How do you implement privacy-compliant tracking?
**Answer**: Obtain explicit consent, respect Do Not Track headers, limit data collection, anonymize data, implement data retention policies.

### 57. What are server-side events?
**Answer**: Events sent from server backend instead of client-side, useful for server-triggered actions and privacy.

### 58. How do you track component rendering performance?
**Answer**: Use performance API and track render times as custom events.

### 59. What is the difference between active users and new users?
**Answer**: New users made first visit, active users engaged with property (varies by definition). Metrics tracked separately.

### 60. How do you implement traffic source tracking?
**Answer**: GA4 automatically tracks source (organic, direct, referral, paid, etc.) via utm parameters and referrer.

### 61. How do you track cart abandonment?
**Answer**:
```javascript
trackEvent('add_to_cart', { items, value });
// Then track if user leaves without purchase
trackEvent('cart_abandon', { cart_value, items_count });
```

### 62. What are attribution models?
**Answer**: Methods for assigning conversion credit to touchpoints. Models: last-click, first-click, linear, time-decay.

### 63. How do you implement product tracking in GA4?
**Answer**: Track items with product properties (id, name, category, brand, price) in commerce events.

### 64. What is the difference between event count and event value?
**Answer**: Event count = number of times event occurred, event value = numerical value assigned to event.

### 65. How do you create custom reports?
**Answer**: Use GA4 exploration tools to create custom dimensions, metrics, and visualizations for specific analysis needs.

### 66. What are Google Analytics 4 limits?
**Answer**: Event name length, parameter count, custom events, user properties have limits. Check documentation for current limits.

### 67. How do you track error messages?
**Answer**:
```javascript
trackEvent('error', { 
  error_type: 'validation',
  error_message: 'Email required',
  page: '/signup'
});
```

### 68. What is the purpose of enhanced ecommerce?
**Answer**: Detailed e-commerce tracking including product impressions, details views, adds to cart, purchases with full product data.

### 69. How do you implement cross-device tracking?
**Answer**: Use User-ID feature to identify same user across devices, requires authentication.

### 70. What are lifecycle events?
**Answer**: Key events in user journey: first_visit, session_start, user_engagement, purchase.

### 71. How do you track feature adoption?
**Answer**:
```javascript
trackEvent('feature_adopted', { 
  feature_name: 'dark_mode',
  adoption_date: new Date().toISOString()
});
```

### 72. What is the difference between events and hits in GA4?
**Answer**: GA4 uses events exclusively (no hits concept like UA). Events are more flexible and powerful.

### 73. How do you implement A/B test tracking?
**Answer**: Track variant assignment, include variant ID in events, compare metrics across variants.

### 74. What is the purpose of BigQuery integration?
**Answer**: Export GA4 data to BigQuery for advanced analysis, custom reporting, and machine learning.

### 75. How do you track third-party integrations?
**Answer**: Track integration events (signup, connect, disconnect) with integration name and details.

### 76. What are custom audiences?
**Answer**: Segments of users created based on specific criteria for analysis or export to other platforms.

### 77. How do you implement conversion value tracking?
**Answer**: Assign monetary value to events for ROI calculation and performance optimization.

### 78. What is the difference between page and pageview?
**Answer**: In GA4, pageview is the event type. Page is the URL parameter within the event.

### 79. How do you track user satisfaction metrics?
**Answer**:
```javascript
trackEvent('user_satisfaction', { 
  satisfaction_score: 8,
  feedback_text: 'Very good'
});
```

### 80. What is the purpose of connected sites?
**Answer**: Linking multiple properties to analyze cross-property user behavior and traffic patterns.

### 81. How do you implement campaign tracking?
**Answer**: Use utm parameters (source, medium, campaign, term, content) in URLs for campaign tracking.

### 82. What are Google Analytics 4 comparison features?
**Answer**: Compare date ranges, user segments, traffic sources, to identify changes and trends.

### 83. How do you track user journeys?
**Answer**: Track sequence of events per user session to understand conversion paths and touchpoints.

### 84. What is the purpose of goals in GA4?
**Answer**: Mark important events as conversions/goals to highlight business outcomes.

### 85. How do you implement quality assurance for tracking?
**Answer**: Use GA4 DebugView, test tracking in development, validate event data before production.

### 86. What are behavioral events?
**Answer**: Events capturing specific user behaviors (engagement, interaction) rather than page structure.

### 87. How do you track microinteractions?
**Answer**: Track small interactions (hover, focus, keyboard shortcuts) as events for UX analysis.

### 88. What is the purpose of data retention settings?
**Answer**: Controls how long GA4 keeps user and event data before deletion, balances privacy and analysis needs.

### 89. How do you track subscription lifecycle?
**Answer**: Track subscription_start, subscription_renew, subscription_cancel events with subscription details.

### 90. What are expected outcomes in GA4?
**Answer**: ML-powered predictions of user behavior (churn, revenue) based on event data.

### 91. How do you implement privacy-preserving analytics?
**Answer**: Use aggregated data, differential privacy, anonymization, user consent, and server-side tracking.

### 92. What is the difference between cohort and segment?
**Answer**: Cohort = users grouped by behavior/date, segment = subset of data for analysis.

### 93. How do you track content recommendations?
**Answer**:
```javascript
trackEvent('view_item', { 
  item_id: itemId,
  recommendation_source: 'homepage',
  recommendation_rank: 3
});
```

### 94. What is the purpose of custom conversions?
**Answer**: Mark specific events as conversions for goal tracking and optimization without code changes.

### 95. How do you implement progressive profiling?
**Answer**: Gradually collect user information across multiple interactions rather than large forms upfront.

### 96. What are consent signals in GA4?
**Answer**: Indicate user consent status (analytics_storage, ad_personalization) affecting data collection and processing.

### 97. How do you track feature flags and experiments?
**Answer**: Include feature flag state and experiment variant in event parameters for analysis.

### 98. What is the purpose of data validation rules?
**Answer**: Automatically filter invalid or suspicious data before it's processed by GA4.

### 99. How do you implement longitudinal analysis?
**Answer**: Track same users over time to measure retention, churn, lifetime value trends.

### 100. What are best practices for Google Analytics 4 in React?
**Answer**:
- Initialize once in App component
- Track page views on route changes
- Use meaningful event names and parameters
- Implement user identification after login
- Track Web Vitals for performance insights
- Set up conversion tracking for key actions
- Test in DebugView before production
- Use custom hooks for consistent tracking
- Implement consent mode for privacy
- Monitor data in real-time for validation
- Regularly review and optimize tracking
- Document tracking implementation
- Keep GA code DRY using utilities
- Use environment-based initialization
- Balance tracking with performance impact

---

## Best Practices

### Event Naming Conventions
```javascript
// Good: snake_case, descriptive
trackEvent('user_signup');
trackEvent('product_view');
trackEvent('checkout_complete');

// Avoid: vague, inconsistent naming
trackEvent('event1');
trackEvent('userAction');
trackEvent('click_button');
```

### Parameter Best Practices
```javascript
// Good: consistent, meaningful parameters
trackEvent('purchase', {
  transaction_id: '12345',
  value: 99.99,
  currency: 'USD',
  items: items
});

// Avoid: too many parameters, unclear names
trackEvent('purchase', {
  a: '12345',
  b: 99.99,
  c: items,
  d: 'extra data'
});
```

### Privacy & Consent
```javascript
// Check consent before initializing
if (hasUserConsent()) {
  initAnalytics();
}

// Respect Do Not Track
if (navigator.doNotTrack !== '1') {
  trackEvent('user_action', {...});
}

// Mask sensitive data
const maskEmail = (email) => {
  const [local] = email.split('@');
  return `${local[0]}***@***.com`;
};
```

### Performance Optimization
```javascript
// Lazy load analytics
const lazyLoadAnalytics = () => {
  setTimeout(() => {
    import('react-ga4').then(module => {
      module.initialize(GA_ID);
    });
  }, 3000);
};

// Batch events
const debouncedTrack = debounce((event) => {
  trackEvent(event.name, event.data);
}, 500);
```

### Testing Analytics
```javascript
// Mock analytics in tests
jest.mock('react-ga4', () => ({
  initialize: jest.fn(),
  event: jest.fn(),
  set: jest.fn()
}));

// Verify tracking is called
it('tracks purchase event', () => {
  render(<PurchaseButton />);
  fireEvent.click(screen.getByRole('button'));
  expect(analytics.event).toHaveBeenCalledWith(
    expect.objectContaining({ action: 'purchase' })
  );
});
```

---

## Common Issues & Solutions

### Analytics Not Tracking
1. Verify measurement ID is correct
2. Check network tab for GA requests
3. Use GA4 DebugView to validate
4. Ensure GA is initialized before tracking
5. Check console for errors

### Missing Page Views
1. Verify page view tracking is in useEffect
2. Confirm route change detection is working
3. Check if tracking is disabled in development
4. Verify GA initialization happens first

### Data Discrepancies
1. Check for duplicate tracking
2. Verify sampling settings
3. Account for filters/exclusions
4. Check data retention settings
5. Compare with other analytics tools

### Performance Issues
1. Lazy load GA script
2. Batch events instead of tracking individually
3. Use debouncing for frequent events
4. Minimize custom parameters
5. Consider server-side tracking

---

## Resources

- [Google Analytics 4 Documentation](https://support.google.com/analytics/answer/10089681)
- [GA4 Event Reference](https://developers.google.com/analytics/devguides/collection/ga4/reference/events)
- [React-GA4 NPM Package](https://www.npmjs.com/package/react-ga4)
- [Google Tag Manager](https://tagmanager.google.com/)
- [GA4 Implementation Guide](https://support.google.com/analytics/answer/9303323)
- [Web Vitals with GA4](https://web.dev/vitals/)
- [BigQuery Export Setup](https://support.google.com/analytics/answer/7029846)
- [GA4 Ecommerce Events](https://developers.google.com/analytics/devguides/collection/ga4/ecommerce)
