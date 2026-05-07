# Image Optimization & Performance Strategy: ADFRAN Cedar

To ensure the Public Gallery remains performant for mobile users in Ghana, the following optimization techniques are implemented across the Firebase/Google Cloud stack.

## 1. Automated Image Resizing (Cloud Functions)
High-resolution workshop photos (often 5MB+) are automatically processed upon upload to Firebase Storage:
- **WebP Conversion**: All images are converted to WebP format, offering ~30% better compression than JPEG without quality loss.
- **Responsive Variants**: Three versions of every piece are generated:
  - `thumbnail` (400px): For the "Recent Uploads" and Gallery grid.
  - `optimized` (1200px): For the Product Detail view.
  - `raw`: Retained in cold storage for archival/printing.

## 2. Global Content Delivery (Firebase Hosting/CDN)
- Images are served via Google's Global CDN, caching assets at edge locations closest to the user to minimize latency.
- **Cache-Control Headers**: Set to `public, max-age=31536000` for furniture assets, as these pieces rarely change once published.

## 3. Frontend Performance (Stitch/IDX)
- **Lazy Loading**: Images below the fold use `loading="lazy"` to prioritize the initial viewport render.
- **Blur-up Placeholders**: Small, low-res (20px) base64 strings are stored in Firestore to provide an instant "blurred" preview while the high-res WebP loads.
- **Aspect Ratio Boxes**: CSS `aspect-ratio` is used to prevent layout shifts (CLS) during image loading.

## 4. Operational Guardrails
- **File Size Limits**: Security rules enforce a 5MB maximum upload size for Francis to prevent accidental uploads of massive raw files that could spike storage costs.
