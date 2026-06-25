---
description: Redesign the Vue 3 app shell from a horizontal top nav bar to a dark vertical sidebar SaaS layout
---

Redesign the application shell layout. Replace the horizontal top nav bar with a dark vertical sidebar on the left. The FilterBar stays as a sticky horizontal strip at the top of the right-side content area.

**MANDATORY:** Per CLAUDE.md rules, delegate ALL .vue file edits to the `vue-expert` agent.

---

## Files to change

| File | What changes |
|------|-------------|
| `client/src/App.vue` | Replace top-nav template block and its CSS with sidebar layout |
| `client/src/components/FilterBar.vue` | Change `top: 70px` → `top: 0` in `.filters-bar` |

No view files, composables, api.js, or router changes needed.

---

## App.vue — new template

Replace the entire `<template>` block with:

```vue
<template>
  <div class="app-layout">
    <aside class="sidebar">
      <div class="sidebar-logo">
        <h1>{{ t('nav.companyName') }}</h1>
        <span class="sidebar-subtitle">{{ t('nav.subtitle') }}</span>
      </div>

      <nav class="sidebar-nav">
        <router-link to="/" :class="{ active: $route.path === '/' }">
          {{ t('nav.overview') }}
        </router-link>
        <router-link to="/inventory" :class="{ active: $route.path === '/inventory' }">
          {{ t('nav.inventory') }}
        </router-link>
        <router-link to="/orders" :class="{ active: $route.path === '/orders' }">
          {{ t('nav.orders') }}
        </router-link>
        <router-link to="/spending" :class="{ active: $route.path === '/spending' }">
          {{ t('nav.finance') }}
        </router-link>
        <router-link to="/demand" :class="{ active: $route.path === '/demand' }">
          {{ t('nav.demandForecast') }}
        </router-link>
        <router-link to="/reports" :class="{ active: $route.path === '/reports' }">
          Reports
        </router-link>
      </nav>

      <div class="sidebar-bottom">
        <LanguageSwitcher />
        <ProfileMenu
          @show-profile-details="showProfileDetails = true"
          @show-tasks="showTasks = true"
        />
      </div>
    </aside>

    <div class="content-wrapper">
      <FilterBar />
      <main class="main-content">
        <router-view />
      </main>
    </div>

    <ProfileDetailsModal
      :is-open="showProfileDetails"
      @close="showProfileDetails = false"
    />

    <TasksModal
      :is-open="showTasks"
      :tasks="tasks"
      @close="showTasks = false"
      @add-task="addTask"
      @delete-task="deleteTask"
      @toggle-task="toggleTask"
    />
  </div>
</template>
```

The `<script>` block is **unchanged** — do not touch it.

---

## App.vue — CSS changes

In the `<style>` block:

**1. Replace the `.app` rule** (currently `display: flex; flex-direction: column; min-height: 100vh`) with:

```css
html, body {
  height: 100%;
}

.app-layout {
  display: flex;
  height: 100vh;
  overflow: hidden;
}
```

**2. Remove these rules entirely** (they belong to the old top nav):
- `.top-nav`
- `.nav-container`
- `.nav-container > .nav-tabs`
- `.nav-container > .language-switcher`
- `.logo`
- `.logo h1`
- `.subtitle`
- `.nav-tabs`
- `.nav-tabs a`
- `.nav-tabs a:hover`
- `.nav-tabs a.active`
- `.nav-tabs a.active::after`

**3. Add these new rules** after the `body` rule:

```css
/* Sidebar */
.sidebar {
  width: 240px;
  flex-shrink: 0;
  background: #0f172a;
  border-right: 1px solid #1e293b;
  display: flex;
  flex-direction: column;
  height: 100vh;
  position: sticky;
  top: 0;
}

.sidebar-logo {
  padding: 24px 20px 20px;
  border-bottom: 1px solid #1e293b;
}

.sidebar-logo h1 {
  font-size: 1rem;
  font-weight: 700;
  color: #f1f5f9;
  letter-spacing: -0.025em;
  margin-bottom: 4px;
}

.sidebar-subtitle {
  font-size: 0.75rem;
  color: #475569;
  font-weight: 400;
}

.sidebar-nav {
  flex: 1;
  padding: 12px 8px;
  display: flex;
  flex-direction: column;
  gap: 2px;
  overflow-y: auto;
}

.sidebar-nav a {
  display: block;
  padding: 10px 16px;
  color: #64748b;
  text-decoration: none;
  font-size: 0.875rem;
  font-weight: 500;
  border-radius: 6px;
  border-left: 2px solid transparent;
  transition: all 0.15s ease;
}

.sidebar-nav a:hover {
  background: rgba(30, 41, 59, 0.6);
  color: #94a3b8;
}

.sidebar-nav a.active {
  background: #1e293b;
  color: #f1f5f9;
  border-left-color: #3b82f6;
}

.sidebar-bottom {
  padding: 16px 12px;
  border-top: 1px solid #1e293b;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

/* Content area */
.content-wrapper {
  flex: 1;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.main-content {
  flex: 1;
  overflow-y: auto;
  padding: 1.5rem 2rem;
}
```

**4. Remove the `max-width` and `margin: 0 auto` from the existing `.main-content` rule** (the sidebar replaces the need for centering — the content area fills the remaining width).

Keep all other existing rules (`.page-header`, `.stats-grid`, `.card`, `.badge`, `.table`, `.loading`, `.error`, etc.) unchanged.

---

## FilterBar.vue — one CSS change

In the `<style scoped>` block, change `.filters-bar`:

```css
/* Before */
top: 70px;

/* After */
top: 0;
```

Also change `z-index: 90` to `z-index: 10` — it no longer needs to compete with a top nav.

No template or script changes in FilterBar.vue.

---

## Verification

After making changes:

1. The frontend is already running at `http://localhost:3000` — open it
2. Confirm a dark sidebar (240px) appears on the left with nav links listed vertically
3. Click each nav link — the active link shows a blue left border highlight and #f1f5f9 text
4. Confirm the FilterBar (4 dropdowns) appears sticky at the top of the right content area
5. Confirm the ProfileMenu and LanguageSwitcher are visible at the bottom of the sidebar
6. Scroll a long page (Dashboard) — sidebar stays fixed while content scrolls
7. Change a FilterBar dropdown — data on the page still updates correctly
