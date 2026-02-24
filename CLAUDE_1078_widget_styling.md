# Product Requirements Document: Chat Widget Mobile Responsiveness & Styling

**Issue**: YaleSites-Internal #1078
**Feature**: Add configurable icon and mobile responsiveness for chat button
**Status**: Phase 1 Complete (Configuration), Phase 2 In Progress (Mobile Responsiveness)
**Date**: 2026-01-15
**Author**: Dave Blanken

---

## Executive Summary

This PRD defines the remaining work needed to complete issue #1078 for the AI Engine chat widget. Phase 1 (configurable icon selection) has been successfully implemented. This document focuses on Phase 2: implementing mobile-responsive behavior for the chat button to improve usability on smaller viewports.

**Current State**:
- ✅ Icon configuration form implemented with Font Awesome options (fa-comments, fa-sparkles)
- ✅ Icon selection stored in Drupal configuration system
- ✅ Icon properly rendered via Twig template with Font Awesome classes
- ✅ Icon sizing adjusted to match original SVG dimensions (3.15rem font-size, 3.75rem width)
- ⚠️ Chat button currently hidden on mobile (display: none below 993px)
- ❌ Mobile responsiveness needs implementation per acceptance criteria

**Desired State**:
- Chat button visible and usable on mobile viewports
- Button automatically shrinks by 20-30% on viewports below 768px width
- Smooth transition between desktop and mobile sizes
- No layout shift during size transitions
- Maintained accessibility and touch target compliance

---

## Business Goals

1. **Improve Mobile User Experience**: Enable mobile users to access the AI chat feature without navigating to a specific page
2. **Reduce Visual Clutter**: Smaller button footprint on mobile reduces interference with page content
3. **Maintain Brand Consistency**: Configurable icons allow YaleSites installations to align with their visual identity
4. **Ensure Accessibility**: Meet WCAG 2.1 AA touch target requirements on all viewport sizes

---

## User Personas

### Primary Persona: Mobile Website Visitor
- **Needs**: Quick access to AI chat assistance on their smartphone
- **Pain Point**: Currently cannot access floating chat button on mobile devices
- **Goal**: Easily launch chat interface with appropriate touch target size

### Secondary Persona: Site Administrator
- **Needs**: Configure chat button appearance to match site branding
- **Pain Point**: Limited control over mobile presentation
- **Goal**: Ensure chat feature works well across all devices

---

## Current Implementation Review

### What's Complete ✅

1. **Backend Configuration** (modules/ai_engine_chat/src/Form/AiEngineChatAdmin.php:56-66)
   - Form field: `floating_button_icon` select dropdown
   - Options: `fa-comments` (default), `fa-sparkles`
   - Stored in `ai_engine_chat.settings` config
   - Proper validation and required field handling

2. **Template Integration** (templates/ai-engine-chat-button.html.twig:5)
   - Icon class passed as Twig variable
   - Renders as: `<i class="fa-light {{ icon_class }}" aria-hidden="true"></i>`
   - Proper ARIA handling (aria-hidden on decorative icon)

3. **Icon Sizing** (css/chat_button.css:36-41)
   - Font size: 3.15rem (matches original SVG dimensions)
   - Width: 3.75rem
   - Margin-right: 0.25em for spacing from text

4. **Desktop Styling** (css/chat_button.css:7-52)
   - Mobile-first CSS approach implemented
   - Shows button only on viewports >= 993px
   - Fixed positioning, z-index management
   - Hover/focus states with color transitions
   - Ed11y accessibility checker integration

### What Needs Implementation ❌

1. **Mobile Display Logic**
   - Change from `display: none` to visible state on mobile
   - Determine appropriate mobile breakpoint (original spec says 768px, current code uses 993px)

2. **Responsive Button Sizing**
   - Implement 20-30% size reduction for mobile viewports
   - Scale button dimensions, padding, font sizes proportionally
   - Ensure text remains readable

3. **Smooth Transitions**
   - Add CSS transitions for size changes at breakpoint
   - Prevent layout shift (consider using `transform: scale()` instead of dimension changes)

4. **Touch Target Compliance**
   - Verify minimum 44x44px touch target on mobile (WCAG 2.1 Level AAA: 44x44px, Level AA: 24x24px)
   - Test with actual mobile devices

5. **Visual Hierarchy**
   - Ensure reduced size doesn't make button too small to notice
   - Test with both icon options (comments and sparkles)

---

## Functional Requirements

### FR-1: Mobile Visibility
**Priority**: P0 (Critical)
**Description**: Chat button must be visible and functional on mobile viewports

**Acceptance Criteria**:
- Button appears on viewports < 993px (or new breakpoint if changed)
- Button remains accessible via keyboard navigation
- Button maintains fixed positioning in viewport
- Button doesn't interfere with page content scrolling

### FR-2: Responsive Size Scaling
**Priority**: P0 (Critical)
**Description**: Button automatically reduces size on smaller viewports

**Acceptance Criteria**:
- Desktop size (≥993px): Current dimensions maintained
- Mobile size (<768px or <993px): 20-30% smaller than desktop
- Scaling applies to:
  - Icon size (currently 3.15rem font-size, 3.75rem width)
  - Padding (currently 1em)
  - Font size (currently 1rem for text)
  - Border radius (currently 0 30px)

**Implementation Note**: Consider two approaches:
1. **Dimension-based**: Adjust padding, font-size values in media query
2. **Transform-based**: Use `transform: scale(0.7)` to avoid layout shift

### FR-3: Smooth Transitions
**Priority**: P1 (High)
**Description**: Size changes occur smoothly without visual jarring

**Acceptance Criteria**:
- Transition duration: 200-300ms (matches existing hover transition at 200ms)
- Transition easing: ease-in-out or similar smooth curve
- No layout shift during transition (prefer transform over dimension changes)
- Transition applies when resizing viewport (e.g., rotating device)

### FR-4: Touch Target Compliance
**Priority**: P0 (Critical)
**Description**: Button meets accessibility touch target requirements

**Acceptance Criteria**:
- Minimum touch target: 44x44px (WCAG 2.1 Level AAA recommended)
- Include padding in touch target calculation
- Verify with browser developer tools mobile emulation
- Test on actual mobile devices (iOS Safari, Android Chrome)

**Current Dimensions Analysis**:
- Desktop: Icon (3.75rem width ≈ 60px) + padding (1em ≈ 16px) + text ≈ 96px+ width, 60px+ height
- Mobile (70% scale): ≈ 67px width, 42px height
- **Recommendation**: 70% scale likely meets minimum, but verify with actual rendering

### FR-5: Accessibility Preservation
**Priority**: P0 (Critical)
**Description**: All accessibility features maintained on mobile

**Acceptance Criteria**:
- aria-label on region remains descriptive: "AI Chat"
- Icon maintains aria-hidden="true" (decorative only)
- Link text remains available to screen readers
- Focus indicator visible and meets contrast requirements
- Keyboard navigation functional (tab to button, enter to activate)

---

## Non-Functional Requirements

### NFR-1: Performance
- CSS-only solution (no JavaScript for size transitions)
- No additional HTTP requests
- Minimal impact on page load time

### NFR-2: Browser Compatibility
- Support modern mobile browsers:
  - iOS Safari 14+
  - Android Chrome 90+
  - Samsung Internet 14+
- Graceful degradation for older browsers (fallback to desktop size if needed)

### NFR-3: Responsive Breakpoints
- Align with YaleSites design system breakpoints if defined
- Current implementation uses 993px (Bootstrap's large breakpoint)
- Original spec mentions 768px (Bootstrap's medium breakpoint)
- **Decision needed**: Confirm breakpoint with UX team

---

## Technical Specifications

### Proposed CSS Implementation

**Option A: Dimension-Based Scaling** (explicit size changes)
```css
/* Mobile: show and scale down */
@media (max-width: 992px) {
  .ai-engine-chat-button {
    display: block;
    /* other mobile styles */
  }

  .ai-engine-chat-button a {
    padding: 0.7em; /* 30% reduction from 1em */
    font-size: 0.85rem; /* ~15% reduction */
    transition: all 200ms ease-in-out;
  }

  .ai-engine-chat-button svg,
  .ai-engine-chat-button i {
    font-size: 2.2rem; /* 30% reduction from 3.15rem */
    width: 2.625rem; /* 30% reduction from 3.75rem */
  }

  .ai-engine-chat-button span {
    max-width: 3.5rem; /* 30% reduction from 5rem */
  }
}
```

**Option B: Transform-Based Scaling** (prevents layout shift)
```css
/* Mobile: show and scale down */
@media (max-width: 992px) {
  .ai-engine-chat-button {
    display: block;
    /* other mobile styles */
  }

  .ai-engine-chat-button a {
    transform: scale(0.75); /* 25% reduction */
    transform-origin: bottom right; /* scale from fixed position corner */
    transition: transform 200ms ease-in-out;
  }
}
```

**Recommendation**: Option B (transform-based) for simpler implementation and guaranteed no layout shift.

### Breakpoint Strategy

**Current State**:
- Desktop: `@media (min-width: 993px)` (mobile-first approach)
- Mobile: implicit (below 993px)

**Options**:
1. Keep 993px (Bootstrap lg breakpoint): Treats tablets as "mobile"
2. Use 768px (Bootstrap md breakpoint): Treats tablets as "desktop"
3. Add two breakpoints: Desktop (993px+), Tablet (768-992px), Mobile (<768px)

**Recommendation**: Two-tier approach (993px breakpoint, smaller scaling below 768px)

### Testing Requirements

**Viewport Testing**:
- Desktop: 1920px, 1440px, 1280px
- Tablet: 992px, 768px (both portrait and landscape)
- Mobile: 414px (iPhone), 375px (common), 320px (small)

**Device Testing**:
- iPhone (Safari): Latest 2 iOS versions
- Android (Chrome): Latest 2 Android versions
- iPad (Safari): Tablet size verification

**Functional Testing**:
- Button visible on all viewport sizes ✓
- Button clickable/tappable ✓
- Touch target ≥44px ✓
- No layout shift on resize ✓
- Smooth transition at breakpoint ✓
- Works with both icon options (fa-comments, fa-sparkles) ✓
- Ed11y position adjustment still works on mobile ✓

---

## Implementation Plan

### Task Breakdown

1. **Determine Breakpoint Strategy** (30 min)
   - Review YaleSites design system documentation
   - Consult with UX team on tablet vs mobile treatment
   - Document decision in ticket comments

2. **Update CSS for Mobile Display** (1 hour)
   - Modify `chat_button.css` media query approach
   - Implement transform-based scaling (Option B recommended)
   - Test transitions at breakpoint
   - Verify Ed11y integration still functions

3. **Accessibility Verification** (1 hour)
   - Test keyboard navigation on mobile emulation
   - Verify touch target size with browser tools
   - Run axe DevTools accessibility scan
   - Test with VoiceOver (iOS) or TalkBack (Android) if possible

4. **Cross-Browser Testing** (1.5 hours)
   - Test on BrowserStack or similar service
   - Verify iOS Safari, Android Chrome, Samsung Internet
   - Document any browser-specific issues
   - Implement fixes if needed

5. **Documentation** (30 min)
   - Update README.md with mobile responsiveness details
   - Add inline CSS comments explaining breakpoint choices
   - Document touch target compliance approach

### Estimated Timeline
**Total: 4.5 hours of development + testing**

**Suggested Schedule**:
- Day 1: Breakpoint decision + CSS implementation + initial testing (2.5 hours)
- Day 2: Accessibility verification + cross-browser testing (2 hours)

---

## Success Metrics

### Completion Criteria (from Issue #1078)
- [x] Chat button icon can be configured via dropdown
- [x] Configuration includes at least 2 Font Awesome icon options
- [x] Default icon remains "comments" for existing installations
- [x] Icon change applies immediately after configuration save
- [ ] **Chat button automatically shrinks by 20-30% on viewports below 768px width** ⬅️ **This PRD's focus**
- [ ] **Mobile button size transition is smooth and doesn't cause layout shift** ⬅️ **This PRD's focus**
- [x] All icon options maintain proper accessibility (aria-label remains descriptive)
- [x] Configuration validates that selected icon exists
- [ ] **Documentation updated in README.md with new configuration option** ⬅️ Needs mobile details

### Quality Metrics
- **Accessibility Score**: axe DevTools scan shows 0 violations for button component
- **Touch Target Size**: ≥44px on mobile (verified via Chrome DevTools device emulation)
- **Browser Compatibility**: Works on 100% of tested browsers (iOS Safari, Android Chrome, Samsung Internet)
- **Performance**: No JavaScript execution for styling, CSS-only solution

---

## Risk Assessment

### Technical Risks

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Transform scale causes blurry rendering on some devices | Medium | Low | Test on multiple devices; fall back to dimension-based scaling if needed |
| Touch target too small at 70% scale | Low | High | Calculate actual dimensions; adjust scale percentage if needed (e.g., 75% or 80%) |
| Breakpoint conflicts with YaleSites theme | Low | Medium | Review theme CSS; coordinate with theme maintainers |
| Button overlaps page content on small screens | Medium | Medium | Test z-index interactions; adjust position if needed |

### Accessibility Risks

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Touch target below WCAG minimum | Low | High | Calculate and verify before implementation |
| Focus indicator lost on mobile | Low | Medium | Test keyboard navigation; ensure outline visible |
| Color contrast insufficient on mobile | Very Low | Medium | Existing colors should maintain contrast; verify with tools |

---

## Open Questions

1. **Breakpoint Strategy**: Should tablets (768-992px) get desktop or mobile sizing?
   - **Recommendation**: Desktop sizing for tablets (use 993px as single breakpoint)
   - **Rationale**: Tablets have more screen real estate; larger button is more usable

2. **Scale Percentage**: 20%, 25%, or 30% reduction?
   - **Recommendation**: 25% (transform: scale(0.75))
   - **Rationale**: Balances size reduction with touch target requirements; 75% of 60px ≈ 45px meets minimum

3. **Button Position on Mobile**: Keep bottom-right or move to bottom-center?
   - **Current**: bottom: 1rem, right: 1%
   - **Recommendation**: Keep bottom-right for consistency with desktop

4. **Hide Text on Mobile**: Should button show icon-only on mobile to save space?
   - **Current**: Both icon and text shown
   - **Recommendation**: Keep text visible for clarity; test with UX team

---

## Dependencies

- None (CSS-only change)
- **Nice-to-have**: Coordination with YaleSites theme team for design system alignment

---

## Appendix

### Related Files
- `modules/ai_engine_chat/css/chat_button.css` - Primary CSS file to modify
- `modules/ai_engine_chat/templates/ai-engine-chat-button.html.twig` - Template (no changes needed)
- `modules/ai_engine_chat/src/Form/AiEngineChatAdmin.php` - Configuration form (complete)
- `modules/ai_engine_chat/config/install/ai_engine_chat.settings.yml` - Config schema (complete)

### References
- YaleSites-Internal Issue #1078: https://github.com/yalesites-org/yalesites-internal/issues/1078
- WCAG 2.1 Success Criterion 2.5.5 (Target Size): https://www.w3.org/WAI/WCAG21/Understanding/target-size.html
- Bootstrap Responsive Breakpoints: https://getbootstrap.com/docs/5.0/layout/breakpoints/

### Git Commits (Phase 1 - Complete)
- `f589061` - style(1078): add icon font styling to match original svg sizing
- `126ad3c` - feat(1078): add configurable chat button icon
- `6c7c07e` - refactor(ui): convert chat button CSS to mobile-first approach
- `811e7ce` - fix(ui): hide chat button on mobile devices

---

**Document Version**: 1.0
**Last Updated**: 2026-01-15
**Next Review**: Upon completion of mobile implementation
