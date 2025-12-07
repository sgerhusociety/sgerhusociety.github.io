# PageSpeed Insights Performance Improvements

## Summary
Based on the PageSpeed Insights analysis (https://pagespeed.web.dev/analysis/https-sgerhusociety-com/891k9kptac?form_factor=mobile), your site had a **Performance Score of 62/100** on mobile.

## Key Metrics (Before Optimization)
- **First Contentful Paint (FCP)**: 5.4s ❌ (Target: < 1.8s)
- **Largest Contentful Paint (LCP)**: 8.0s ❌ (Target: < 2.5s)
- **Total Blocking Time (TBT)**: 20ms ✅
- **Cumulative Layout Shift (CLS)**: 0 ✅
- **Speed Index**: 5.5s ❌

## Implemented Optimizations

### 1. Resource Preloading ✅
**File**: `_layouts/default.html`
- Added preload directives for critical CSS
- Added preload for above-the-fold images (logo.webp, header.webp)
- **Expected Impact**: Reduced FCP by 0.5-1s, LCP by 1-2s

```html
<link rel="preload" href="/assets/css/styles.css" as="style">
<link rel="preload" href="/files/logo.webp" as="image" type="image/webp">
<link rel="preload" href="/files/header.webp" as="image" type="image/webp">
```

### 2. Image Optimization ✅
**Files**: `_includes/center-caption-image.html`, `_includes/youtubePlayer.html`
- Added explicit `width` and `height` attributes to all images
- Added `aspect-ratio` CSS to maintain layout during loading
- Implemented responsive iframe containers with proper aspect ratios
- **Expected Impact**: Eliminated CLS issues, improved LCP

**center-caption-image.html**:
```html
<img width="800" height="600" style="aspect-ratio: 800 / 600" />
```

**youtubePlayer.html**:
```html
<div style="position: relative; padding-bottom: 56.25%; height: 0;">
    <iframe width="560" height="315" style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" />
</div>
```

### 3. JavaScript Optimization ✅
**File**: `_layouts/default.html`
- Moved Google Analytics to end of body (non-blocking)
- Optimized back-to-top button script with:
  - `requestAnimationFrame` for scroll throttling
  - Passive event listeners for better scroll performance
  - Self-executing function to avoid global scope pollution
- **Expected Impact**: Reduced TBT, improved FCP

```javascript
window.addEventListener('scroll', function() {
    if (!ticking) {
        window.requestAnimationFrame(updateBackToTop);
        ticking = true;
    }
}, { passive: true });
```

### 4. Animation Performance ✅
**File**: `_sass/main.scss`
- Added `will-change: transform, opacity` to back-to-top button
- Added `contain: layout style paint` for better rendering isolation
- **Expected Impact**: Fixed "non-composited animations" warning

```scss
.back-to-top {
    will-change: transform, opacity;
    contain: layout style paint;
}
```

### 5. Cache Headers Enhancement ✅
**File**: `_headers`
- Added explicit `Content-Type` headers for all image formats
- Added WebP-specific caching rules
- Optimized cache lifetimes:
  - Static assets (images, CSS, JS): 1 year (immutable)
  - HTML: 1 hour
- **Expected Impact**: Improved cache hit ratio, reduced bandwidth

```
/files/*.webp
  Cache-Control: public, max-age=31536000, immutable
  Content-Type: image/webp
```

## Expected Performance Improvements

After these optimizations, you should see:

1. **FCP**: Improved from 5.4s → ~3.5-4s (34% improvement)
   - Preloading critical resources
   - Moving analytics to end of body

2. **LCP**: Improved from 8.0s → ~5-6s (25-37% improvement)
   - Preloading hero images
   - Explicit image dimensions
   - Better caching

3. **TBT**: Already good at 20ms, minimal change expected

4. **CLS**: Maintained at 0 with explicit dimensions

5. **Overall Score**: Expected improvement from 62 → **75-80** 🎯

## Next Steps for Further Optimization

### High Priority (Not Yet Implemented)
1. **Image Compression**: Convert all JPG images to WebP format
   - Current savings: ~1,017 KiB
   - Tools: `cwebp`, imagemin, or Squoosh

2. **Image Sizing**: Serve responsive images with `srcset`
   ```html
   <img srcset="image-320.webp 320w, image-640.webp 640w, image-1024.webp 1024w"
        sizes="(max-width: 640px) 100vw, 640px">
   ```

3. **CSS Critical Path**: Extract and inline critical CSS
   - Tools: Critical, Penthouse, or PurgeCSS

### Medium Priority
4. **Font Optimization**: 
   - Subset Chinese fonts to reduce size
   - Use `font-display: swap` for custom fonts
   - Consider using variable fonts

5. **JavaScript Minification**: 
   - Minify inline scripts
   - Consider removing unused JavaScript

6. **CDN Implementation**:
   - Use CloudFlare or similar CDN
   - Enable HTTP/3 and Brotli compression

### Low Priority
7. **Service Worker**: Implement for offline support and faster repeat visits
8. **HTTP/2 Server Push**: Push critical resources automatically
9. **Lazy Load Social Media Widgets**: Defer Facebook embeds until user interaction

## Testing & Monitoring

1. **Re-test on PageSpeed Insights** after deployment:
   - https://pagespeed.web.dev/
   
2. **Monitor Real User Metrics (RUM)**:
   - Use Google Analytics Core Web Vitals report
   - Consider adding Web Vitals tracking script

3. **Test on Multiple Devices**:
   - Use Chrome DevTools mobile emulation
   - Test on real devices (slow 3G/4G)

4. **Validate Changes**:
   - Check browser console for errors
   - Verify images load correctly
   - Test responsive layouts

## Files Modified

1. `_layouts/default.html` - Preloading, script optimization
2. `_includes/center-caption-image.html` - Image dimensions
3. `_includes/youtubePlayer.html` - Iframe dimensions
4. `_sass/main.scss` - Animation performance
5. `_headers` - Cache optimization

## Deployment Checklist

- [ ] Test site locally with Jekyll
- [ ] Verify all images have dimensions
- [ ] Check browser console for warnings
- [ ] Test on mobile device
- [ ] Deploy to GitHub Pages
- [ ] Wait 5-10 minutes for CDN cache
- [ ] Re-run PageSpeed Insights
- [ ] Compare before/after scores

---

**Generated**: December 7, 2025
**PageSpeed Report**: https://pagespeed.web.dev/analysis/https-sgerhusociety-com/891k9kptac?form_factor=mobile
