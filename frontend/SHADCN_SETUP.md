# shadcn/ui Setup Complete

## What Was Installed

### Core Dependencies
- ✅ Tailwind CSS v3.4.x
- ✅ PostCSS & Autoprefixer
- ✅ class-variance-authority
- ✅ clsx
- ✅ tailwind-merge
- ✅ tailwindcss-animate
- ✅ lucide-react (icon library)

### Configuration Files Created
- ✅ `tailwind.config.js` - Tailwind configuration with shadcn/ui theme
- ✅ `components.json` - shadcn/ui CLI configuration
- ✅ `src/lib/utils.ts` - Utility functions (cn helper)
- ✅ `src/index.css` - CSS with Tailwind directives and theme variables

### TypeScript Configuration
- ✅ Path aliases configured (`@/*` maps to `./src/*`)
- ✅ Updated `tsconfig.app.json` with baseUrl and paths
- ✅ Updated `vite.config.ts` with path resolution

### Installed Components
The following shadcn/ui components are ready to use:
- ✅ Button (`src/components/ui/button.tsx`)
- ✅ Card (`src/components/ui/card.tsx`)
- ✅ Input (`src/components/ui/input.tsx`)
- ✅ Label (`src/components/ui/label.tsx`)
- ✅ Dialog (`src/components/ui/dialog.tsx`)
- ✅ Badge (`src/components/ui/badge.tsx`)
- ✅ Avatar (`src/components/ui/avatar.tsx`)

## How to Use

### Importing Components
```tsx
import { Button } from "@/components/ui/button"
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card"

function MyComponent() {
  return (
    <Card>
      <CardHeader>
        <CardTitle>Hello World</CardTitle>
      </CardHeader>
      <CardContent>
        <Button>Click me</Button>
      </CardContent>
    </Card>
  )
}
```

### Adding More Components
To add more shadcn/ui components:

```bash
npx shadcn@latest add [component-name]
```

Popular components you'll need for Everything-Tracker:
```bash
# Forms
npx shadcn@latest add form select textarea checkbox

# Navigation
npx shadcn@latest add tabs dropdown-menu navigation-menu

# Data Display
npx shadcn@latest add table calendar chart

# Feedback
npx shadcn@latest add toast alert progress

# Overlays
npx shadcn@latest add popover tooltip sheet
```

After adding components, move them from `@/components/ui/` to `src/components/ui/`:
```bash
mv @/components/ui/* src/components/ui/ && rm -rf @
```

## Theme Customization

### Colors
The theme uses CSS variables defined in `src/index.css`. You can customize:
- Primary, secondary, accent colors
- Background and foreground colors
- Border, input, ring colors
- Destructive (error) colors

### Dark Mode
Dark mode is enabled and uses the `.dark` class. To toggle dark mode:

```tsx
// Add this to your root component
<html className="dark">
  {/* or use a state/context to toggle */}
</html>
```

### Border Radius
Adjust the `--radius` variable in `src/index.css` to change the roundedness of components.

## Running the App

```bash
# Development
npm run dev

# Build
npm run build

# Preview build
npm run preview
```

## Next Steps for Everything-Tracker

Now that shadcn/ui is set up, you can:

1. **Install form components** for activity tracking
   ```bash
   npx shadcn@latest add form select calendar
   ```

2. **Install data display components** for analytics
   ```bash
   npx shadcn@latest add table chart tabs
   ```

3. **Set up routing** with React Router for multiple pages

4. **Create layout components** (Navbar, Sidebar, Footer)

5. **Build feature components** (ActivityCard, HeatmapCalendar, etc.)

## Useful Resources

- [shadcn/ui Documentation](https://ui.shadcn.com)
- [Tailwind CSS Documentation](https://tailwindcss.com/docs)
- [Lucide Icons](https://lucide.dev) - Icon library included
- [Radix UI](https://www.radix-ui.com) - Primitive components used by shadcn/ui

## Troubleshooting

### Components not found after installation
If shadcn CLI creates components in `@/components/ui/`, move them manually:
```bash
mv @/components/ui/* src/components/ui/ && rm -rf @
```

### Import errors
Make sure your imports use the `@/` prefix:
```tsx
import { Button } from "@/components/ui/button" // ✅ Correct
import { Button } from "./components/ui/button" // ❌ Wrong
```

### Styling not applied
Ensure `src/index.css` is imported in `src/main.tsx`:
```tsx
import './index.css' // ✅ Required
```