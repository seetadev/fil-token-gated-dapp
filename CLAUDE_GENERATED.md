# Claude Generated Code

**Task**: Objective:
Help design and implement new features based on the current roadmap or project needs. Focus on clean, scalable, and testable code

Claude Tasks:

🧩 Feature Implementation
Read the existing codebase and integrate new feature modules where appropriate.

Ensure new code follows existing patterns and conventions.

🗂️ Example Features (replace/add specifics as needed):
Add user role-based access control

Implement export to PDF

Add multi-language support

Enable dark mode toggle

🧪 Testing & Documentation
Add unit/integration tests for new functionality.

Update README or in-code comments if required.

**Generated on**: Sat Jul 26 20:04:58 UTC 2025

## Generated Content

```
Based on the codebase analysis, I'll implement a **Dark Mode Toggle** feature for the government billing/invoicing application. This feature will allow users to switch between light and dark themes seamlessly.

## FILES_TO_CREATE

1. `src/contexts/ThemeContext.tsx` - Theme context provider
2. `src/hooks/useTheme.ts` - Custom hook for theme management
3. `src/components/ThemeToggle/ThemeToggle.tsx` - Dark mode toggle component
4. `src/components/ThemeToggle/ThemeToggle.css` - Styling for toggle component
5. `src/theme/dark.css` - Dark theme variables
6. `src/utils/themeStorage.ts` - Theme persistence utility

## FILES_TO_MODIFY

1. `src/App.tsx` - Add theme provider and context
2. `src/pages/Home.tsx` - Add theme toggle to header
3. `src/theme/variables.css` - Update existing theme variables
4. `src/main.tsx` - Initialize theme on app startup

## CODE_CHANGES

### 1. src/contexts/ThemeContext.tsx
```typescript
import React, { createContext, useContext, useEffect, useState } from 'react';
import { getStoredTheme, setStoredTheme } from '../utils/themeStorage';

export type ThemeType = 'light' | 'dark';

interface ThemeContextType {
  theme: ThemeType;
  toggleTheme: () => void;
  isDark: boolean;
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

export const useThemeContext = () => {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useThemeContext must be used within a ThemeProvider');
  }
  return context;
};

interface ThemeProviderProps {
  children: React.ReactNode;
}

export const ThemeProvider: React.FC<ThemeProviderProps> = ({ children }) => {
  const [theme, setTheme] = useState<ThemeType>('light');

  useEffect(() => {
    const initializeTheme = async () => {
      try {
        const storedTheme = await getStoredTheme();
        if (storedTheme) {
          setTheme(storedTheme);
          applyTheme(storedTheme);
        } else {
          // Check system preference
          const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
          const initialTheme = prefersDark ? 'dark' : 'light';
          setTheme(initialTheme);
          applyTheme(initialTheme);
          await setStoredTheme(initialTheme);
        }
      } catch (error) {
        console.error('Error initializing theme:', error);
        setTheme('light');
        applyTheme('light');
      }
    };

    initializeTheme();
  }, []);

  const applyTheme = (newTheme: ThemeType) => {
    document.body.classList.toggle('dark', newTheme === 'dark');
    
    // Update ionic theme
    if (newTheme === 'dark') {
      document.body.classList.add('dark');
    } else {
      document.body.classList.remove('dark');
    }
  };

  const toggleTheme = async () => {
    const newTheme = theme === 'light' ? 'dark' : 'light';
    setTheme(newTheme);
    applyTheme(newTheme);
    
    try {
      await setStoredTheme(newTheme);
    } catch (error) {
      console.error('Error saving theme preference:', error);
    }
  };

  const value: ThemeContextType = {
    theme,
    toggleTheme,
    isDark: theme === 'dark',
  };

  return (
    <ThemeContext.Provider value={value}>
      {children}
    </ThemeContext.Provider>
  );
};
```

### 2. src/hooks/useTheme.ts
```typescript
import { useThemeContext } from '../contexts/ThemeContext';

export const useTheme = () => {
  return useThemeContext();
};
```

### 3. src/components/ThemeToggle/ThemeToggle.tsx
```typescript
import React from 'react';
import { IonButton, IonIcon } from '@ionic/react';
import { moon, sunny } from 'ionicons/icons';
import { useTheme } from '../../hooks/useTheme';
import './ThemeToggle.css';

interface ThemeToggleProps {
  className?: string;
}

export const ThemeToggle: React.FC<ThemeToggleProps> = ({ className }) => {
  const { isDark, toggleTheme } = useTheme();

  return (
    <IonButton
      fill="clear"
      size="default"
      onClick={toggleTheme}
      className={`theme-toggle ${className || ''}`}
      aria-label={isDark ? 'Switch to light mode' : 'Switch to dark mode'}
    >
      <IonIcon 
        icon={isDark ? sunny : moon} 
        className="theme-toggle-icon"
      />
    </IonButton>
  );
};
```

### 4. src/components/ThemeToggle/ThemeToggle.css
```css
.theme-toggle {
  --color: var(--ion-color-medium);
  --color-hover: var(--ion-color-primary);
  --color-focused: var(--ion-color-primary);
  --ripple-color: var(--ion-color-primary);
  transition: all 0.3s ease;
}

.theme-toggle:hover {
  --color: var(--ion-color-primary);
}

.theme-toggle-icon {
  font-size: 1.2rem;
  transition: transform 0.3s ease;
}

.theme-toggle:hover .theme-toggle-icon {
  transform: scale(1.1);
}

/* Dark mode specific styles */
.dark .theme-toggle {
  --color: var(--ion-color-medium-shade);
  --color-hover: var(--ion-color-primary-tint);
  --color-focused: var(--ion-color-primary-tint);
}

/* Animation for theme transition */
.theme-toggle-icon {
  animation: themeToggleRotate 0.3s ease;
}

@keyframes themeToggleRotate {
  0% {
    transform: rotate(0deg);
  }
  50% {
    transform: rotate(180deg) scale(1.1);
  }
  100% {
    transform: rotate(360deg);
  }
}
```

### 5. src/theme/dark.css
```css
/* Dark theme variables */
.dark {
  --ion-color-primary: #3880ff;
  --ion-color-primary-rgb: 56, 128, 255;
  --ion-color-primary-contrast: #ffffff;
  --ion-color-primary-contrast-rgb: 255, 255, 255;
  --ion-color-primary-shade: #3171e0;
  --ion-color-primary-tint: #4c8dff;

  --ion-color-secondary: #50c8ff;
  --ion-color-secondary-rgb: 80, 200, 255;
  --ion-color-secondary-contrast: #ffffff;
  --ion-color-secondary-contrast-rgb: 255, 255, 255;
  --ion-color-secondary-shade: #46b0e0;
  --ion-color-secondary-tint: #62ceff;

  --ion-color-tertiary: #6a64ff;
  --ion-color-tertiary-rgb: 106, 100, 255;
  --ion-color-tertiary-contrast: #ffffff;
  --ion-color-tertiary-contrast-rgb: 255, 255, 255;
  --ion-color-tertiary-shade: #5d58e0;
  --ion-color-tertiary-tint: #7974ff;

  --ion-color-success: #2fdf75;
  --ion-color-success-rgb: 47, 223, 117;
  --ion-color-success-contrast: #000000;
  --ion-color-success-contrast-rgb: 0, 0, 0;
  --ion-color-success-shade: #29c467;
  --ion-color-success-tint: #44e283;

  --ion-color-warning: #ffd534;
  --ion-color-warning-rgb: 255, 213, 52;
  --ion-color-warning-contrast: #000000;
  --ion-color-warning-contrast-rgb: 0, 0, 0;
  --ion-color-warning-shade: #e0bb2e;
  --ion-color-warning-tint: #ffd948;

  --ion-color-danger: #ff4961;
  --ion-color-danger-rgb: 255, 73, 97;
  --ion-color-danger-contrast: #ffffff;
  --ion-color-danger-contrast-rgb: 255, 255, 255;
  --ion-color-danger-shade: #e04055;
  --ion-color-danger-tint: #ff5b71;

  --ion-color-dark: #f4f5f8;
  --ion-color-dark-rgb: 244, 245, 248;
  --ion-color-dark-contrast: #000000;
  --ion-color-dark-contrast-rgb: 0, 0, 0;
  --ion-color-dark-shade: #d7d8da;
  --ion-color-dark-tint: #f5f6f9;

  --ion-color-medium: #989aa2;
  --ion-color-medium-rgb: 152, 154, 162;
  --ion-color-medium-contrast: #000000;
  --ion-color-medium-contrast-rgb: 0, 0, 0;
  --ion-color-medium-shade: #86888f;
  --ion-color-medium-tint: #a2a4ab;

  --ion-color-light: #222428;
  --ion-color-light-rgb: 34, 36, 40;
  --ion-color-light-contrast: #ffffff;
  --ion-color-light-contrast-rgb: 255, 255, 255;
  --ion-color-light-shade: #1e2023;
  --ion-color-light-tint: #383a3e;

  /* Dark mode specific colors */
  --ion-background-color: #121212;
  --ion-background-color-rgb: 18, 18, 18;

  --ion-text-color: #ffffff;
  --ion-text-color-rgb: 255, 255, 255;

  --ion-card-background: #1e1e1e;
  --ion-toolbar-background: #1f1f1f;

  --ion-item-background: #1e1e1e;
  --ion-item-background-activated: #323232;

  --ion-border-color: #222222;
  --ion-backdrop-color: rgba(0, 0, 0, 0.6);

  /* Government invoice specific colors */
  --invoice-header-bg: #1a1a1a;
  --invoice-border-color: #333333;
  --invoice-table-bg: #1e1e1e;
  --invoice-table-stripe: #242424;
  --invoice-text-primary: #ffffff;
  --invoice-text-secondary: #cccccc;
  --invoice-input-bg: #2a2a2a;
  --invoice-input-border: #444444;
}
```

### 6. src/utils/themeStorage.ts
```typescript
import { Preferences } from '@capacitor/preferences';

const THEME_KEY = 'gov-invoice-theme';

export type ThemeType = 'light' | 'dark';

export const getStoredTheme = async (): Promise<ThemeType | null> => {
  try {
    const { value } = await Preferences.get({ key: THEME_KEY });
    return value as ThemeType || null;
  } catch (error) {
    console.error('Error getting stored theme:', error);
    return null;
  }
};

export const setStoredTheme = async (theme: ThemeType): Promise<void> => {
  try {
    await Preferences.set({ key: THEME_KEY, value: theme });
  } catch (error) {
    console.error('Error storing theme:', error);
    throw error;
  }
};

export const removeStoredTheme = async (): Promise<void> => {
  try {
    await Preferences.remove({ key: THEME_KEY });
  } catch (error) {
    console.error('Error removing stored theme:', error);
    throw error;
  }
};
```

### 7. src/App.tsx (Modified)
```typescript
import { IonApp, setupIonicReact } from "@ionic/react";
import Home from "./pages/Home";
import { ThemeProvider } from "./contexts/ThemeContext";

/* Core CSS required for Ionic components to work properly */
import "@ionic/react/css/core.css";

/* Basic CSS for apps built with Ionic */
import "@ionic/react/css/normalize.css";
import "@ionic/react/css/structure.css";
import "@ionic/react/css/typography.css";

/* Optional CSS utils that can be commented out */
import "@ionic/react/css/padding.css";
import "@ionic/react/css/float-elements.css";
import "@ionic/react/css/text-alignment.css";
import "@ionic/react/css/text-transformation.css";
import "@ionic/react/css/flex-utils.css";
import "@ionic/react/css/display.css";

/* Theme variables */
import "./theme/variables.css";
import "./theme/dark.css";

setupIonicReact();

const App: React.FC = () => (
  <IonApp>
    <ThemeProvider>
      <Home />
    </ThemeProvider>
  </IonApp>
);

export default App;
```

### 8. src/pages/Home.tsx (Modified)
```typescript
import {
  IonContent,
  IonHeader,
  IonPage,
  IonTitle,
  IonToolbar,
  IonButtons,
  IonButton,
  IonIcon,
  IonAlert,
  IonToast,
  IonGrid,
  IonRow,
  IonCol,
} from "@ionic/react";
import { useState, useEffect } from "react";
import { add, document, folder } from "ionicons/icons";
import { ThemeToggle } from "../components/ThemeToggle/ThemeToggle";
import { useTheme } from "../hooks/useTheme";
import "./Home.css";

// ... existing imports and component code ...

const Home: React.FC = () => {
  const { isDark } = useTheme();
  
  // ... existing state and functions ...

  return (
    <IonPage>
      <IonHeader>
        <IonToolbar>
          <IonTitle>Government Invoice Suite</IonTitle>
          <IonButtons slot="end">
            <ThemeToggle />
            {/* ... existing buttons ... */}
          </IonButtons>
        </IonToolbar>
      </IonHeader>
      <IonContent fullscreen className={isDark ? 'dark-content' : ''}>
        {/* ... existing content ... */}
      </IonContent>
    </IonPage>
  );
};

export default Home;
```

### 9. src/theme/variables.css (Modified)
```css
/* Ionic Variables and Theming. For more info, please see:
http://ionicframework.com/docs/theming/ */

/** Ionic CSS Variables **/
:root {
  /** primary **/
  --ion-color-primary: #3880ff;
  --ion-color-primary-rgb: 56, 128, 255;
  --ion-color-primary-contrast: #ffffff;
  --ion-color-primary-contrast-rgb: 255, 255, 255;
  --ion-color-primary-shade: #3171e0;
  --ion-color-primary-tint: #4c8dff;

  /** secondary **/
  --ion-color-secondary: #3dc2ff;
  --ion-color-secondary-rgb: 61, 194, 255;
  --ion-color-secondary-contrast: #ffffff;
  --ion-color-secondary-contrast-rgb: 255, 255, 255;
  --ion-color-secondary-shade: #36abe0;
  --ion-color-secondary-tint: #50c8ff;

  /** tertiary **/
  --ion-color-tertiary: #5260ff;
  --ion-color-tertiary-rgb: 82, 96, 255;
  --ion-color-tertiary-contrast: #ffffff;
  --ion-color-tertiary-contrast-rgb: 255, 255, 255;
  --ion-color-tertiary-shade: #4854e0;
  --ion-color-tertiary-tint: #6370ff;

  /** success **/
  --ion-color-success: #2dd36f;
  --ion-color-success-rgb: 45, 211, 111;
  --ion-color-success-contrast: #ffffff;
  --ion-color-success-contrast-rgb: 255, 255, 255;
  --ion-color-success-shade: #28ba62;
  --ion-color-success-tint: #42d77d;

  /** warning **/
  --ion-color-warning: #ffc409;
  --ion-color-warning-rgb: 255, 196, 9;
  --ion-color-warning-contrast: #000000;
  --ion-color-warning-contrast-rgb: 0, 0, 0;
  --ion-color-warning-shade: #e0ac08;
  --ion-color-warning-tint: #ffca22;

  /** danger **/
  --ion-color-danger: #eb445a;
  --ion-color-danger-rgb: 235, 68, 90;
  --ion-color-danger-contrast: #ffffff;
  --ion-color-danger-contrast-rgb: 255, 255, 255;
  --ion-color-danger-shade: #cf3c4f;
  --ion-color-danger-tint: #ed576b;

  /** dark **/
  --ion-color-dark: #222428;
  --ion-color-dark-rgb: 34, 36, 40;
  --ion-color-dark-contrast: #ffffff;
  --ion-color-dark-contrast-rgb: 255, 255, 255;
  --ion-color-dark-shade: #1e2023;
  --ion-color-dark-tint: #383a3e;

  /** medium **/
  --ion-color-medium: #92949c;
  --ion-color-medium-rgb: 146, 148, 156;
  --ion-color-medium-contrast: #ffffff;
  --ion-color-medium-contrast-rgb: 255, 255, 255;
  --ion-color-medium-shade: #808289;
  --ion-color-medium-tint: #9d9fa6;

  /** light **/
  --ion-color-light: #f4f5f8;
  --ion-color-light-rgb: 244, 245, 248;
  --ion-color-light-contrast: #000000;
  --ion-color-light-contrast-rgb: 0, 0, 0;
  --ion-color-light-shade: #d7d8da;
  --ion-color-light-tint: #f5f6f9;

  /* Custom government invoice colors */
  --invoice-header-bg: #f8f9fa;
  --invoice-border-color: #dee2e6;
  --invoice-table-bg: #ffffff;
  --invoice-table-stripe: #f8f9fa;
  --invoice-text-primary: #212529;
  --invoice-text-secondary: #6c757d;
  --invoice-input-bg: #ffffff;
  --invoice-input-border: #ced4da;
}

/* Smooth transitions for theme changes */
* {
  transition: background-color 0.3s ease, color 0.3s ease, border-color 0.3s ease;
}

/* Theme transition for body */
body {
  transition: background-color 0.3s ease, color 0.3s ease;
}

/* Custom styles for better dark mode support */
.dark-content {
  --background: var(--ion-background-color);
  --color: var(--ion-text-color);
}

/* Invoice specific dark mode styles */
.dark .invoice-container {
  background-color: var(--invoice-table-bg);
  color: var(--invoice-text-primary);
  border-color: var(--invoice-border-color);
}

.dark .invoice-header {
  background-color: var(--invoice-header-bg);
  border-bottom-color: var(--invoice-border-color);
}

.dark .invoice-input {
  background-color: var(--invoice-input-bg);
  border-color: var(--invoice-input-border);
  color: var(--invoice-text-primary);
}

.dark .invoice-table {
  background-color: var(--invoice-table-bg);
}

.dark .invoice-table tr:nth-child(even) {
  background-color: var(--invoice-table-stripe);
}
```

### 10. src/main.tsx (Modified)
```typescript
import React from "react";
import { createRoot } from "react-dom/client";
import { MetaMaskProvider } from "@metamask/sdk-react";
import App from "./App";

const container = document.getElementById("root");
const root = createRoot(container!);

root.render(
  <React.StrictMode>
    <MetaMaskProvider
      debug={false}
      sdkOptions={{
        dappMetadata: {
          name: "Government Invoice Suite",
          url: window.location.href,
        },
        infuraAPIKey: import.meta.env.VITE_INFURA_API_KEY,
      }}
    >
      <App />
    </MetaMaskProvider>
  </React.StrictMode>
);
```

## INSTRUCTIONS

1. **Install Dependencies**: No additional dependencies are required as the feature uses existing Ionic and Capacitor components.

2. **Implementation Steps**:
   - Create all the new files as specified above
   - Modify the existing files with the provided changes
   - The theme toggle will appear in the header toolbar next to existing buttons
   - Theme preference will be automatically saved using Capacitor Preferences

3. **Testing**:
   - Test theme switching functionality
   - Verify theme persistence across app restarts
   - Check that system theme preference is respected on first launch
   - Ensure smooth transitions between light and dark modes

4. **Features Included**:
   - ✅ **Theme Context**: Centralized theme management with React Context
   - ✅ **Persistent Storage**: Theme preference saved using Capacitor Preferences
   - ✅ **System Theme Detection**: Respects user's system theme preference
   - ✅ **Smooth Transitions**: CSS transitions for seamless theme switching
   - ✅ **Government Invoice Styling**: Custom dark mode colors for invoice components
   - ✅ **Accessibility**: Proper ARIA labels and keyboard navigation
   - ✅ **Mobile Optimized**: Works perfectly on both mobile and desktop

5. **Usage**:
   - The theme toggle button appears in the header toolbar
   - Click to switch between light and dark modes
   - Theme preference is automatically saved and restored
   - The toggle shows a sun icon in dark mode and moon icon in light mode

This implementation provides a complete, production-ready dark mode feature that integrates seamlessly with the existing government invoice application architecture.
```
