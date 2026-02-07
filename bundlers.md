# Webpack, Rspack & Bundlers - 100 Most Asked Interview Questions

## Table of Contents
1. [Webpack Fundamentals](#webpack-fundamentals)
2. [Webpack Advanced Concepts](#webpack-advanced-concepts)
3. [Rspack](#rspack)
4. [Other Bundlers](#other-bundlers)
5. [Performance & Optimization](#performance--optimization)
6. [Interview Q&A](#interview-qa)

---

## Webpack Fundamentals

### What is Webpack?
Webpack is a static module bundler for JavaScript applications. It takes modules with dependencies and generates static assets representing those modules.

### Key Concepts

**Entry**: The entry point tells webpack where to start bundling. Default is `./src/index.js`.

**Output**: Tells webpack where to emit the bundles it creates and how to name these files.

**Loaders**: Transform non-JavaScript modules into valid JavaScript modules before webpack processes them.

**Plugins**: Extend webpack's functionality beyond module bundling.

**Mode**: Tells webpack to use its built-in optimizations. Options: `development`, `production`, `none`.

---

## Webpack Advanced Concepts

### Code Splitting
Breaking code into multiple chunks that can be loaded on demand or in parallel.

```javascript
// Dynamic import
import('module').then(module => {
  // use module
});

// Entry points
module.exports = {
  entry: {
    index: './src/index.js',
    about: './src/about.js'
  }
};
```

### Tree Shaking
Removing unused code from the final bundle. Works best with ES6 modules.

```javascript
// webpack.config.js
module.exports = {
  mode: 'production',
  optimization: {
    usedExports: true
  }
};
```

### Module Federation
Allows separate builds to form a single application at runtime.

```javascript
const ModuleFederationPlugin = require('webpack/lib/container/ModuleFederationPlugin');

module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'app1',
      filename: 'remoteEntry.js',
      remotes: {
        app2: 'app2@http://localhost:3002/remoteEntry.js'
      },
      exposes: {
        './Component': './src/Component'
      },
      shared: require('./package.json').dependencies
    })
  ]
};
```

### Caching
Webpack can generate consistent hashes for cached content.

```javascript
module.exports = {
  output: {
    filename: '[name].[contenthash].js',
    chunkFilename: '[id].[contenthash].js'
  }
};
```

---

## Rspack

### What is Rspack?
Rspack is a Rust-based bundler that provides 5-10x faster bundling than webpack while maintaining webpack compatibility.

### Key Features
- Written in Rust using SWC for fast JavaScript parsing and transformation
- Drop-in replacement for webpack in most cases
- Better caching strategy
- Faster incremental builds
- Smaller bundle sizes
- Better dev server performance

### Basic Configuration
```javascript
// rspack.config.js
module.exports = {
  entry: './src/index.js',
  output: {
    path: __dirname + '/dist',
    filename: '[name].js'
  },
  module: {
    rules: [
      {
        test: /\.jsx?$/,
        use: {
          loader: 'builtin:swc-loader',
          options: {
            jsc: {
              parser: {
                jsx: true
              }
            }
          }
        }
      }
    ]
  }
};
```

### Webpack Compatibility
Rspack is compatible with most webpack plugins and loaders but may require some adjustments for advanced features.

---

## Other Bundlers

### Vite
Modern frontend build tool built on native ES modules.

**Advantages**:
- Lightning fast dev server (uses native ES modules)
- Optimized production builds with Rollup
- HMR out of the box
- Framework agnostic

```javascript
// vite.config.js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  build: {
    rollupOptions: {
      output: {
        manualChunks: {}
      }
    }
  }
})
```

### Parcel
Zero-config bundler that handles most configurations automatically.

**Advantages**:
- Minimal configuration needed
- Good build performance
- Automatic code splitting
- Built-in dev server

### Esbuild
Extremely fast JavaScript bundler written in Go.

**Characteristics**:
- 10-100x faster than other bundlers
- Minimal feature set
- Good for libraries
- No built-in dev server (requires separate tool)

### Turbopack
Incremental bundler created by Vercel, written in Rust.

**Features**:
- Ultra-fast incremental builds
- Next.js integration
- Streaming architecture
- Still in development/experimental

---

## Performance & Optimization

### Bundle Analysis
```javascript
// webpack.config.js
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

module.exports = {
  plugins: [
    new BundleAnalyzerPlugin()
  ]
};
```

### Lazy Loading
```javascript
// Dynamic imports for route-based code splitting
const Home = lazy(() => import('./pages/Home'));
const About = lazy(() => import('./pages/About'));
```

### Splitting Vendor Code
```javascript
module.exports = {
  optimization: {
    splitChunks: {
      chunks: 'all',
      cacheGroups: {
        vendor: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          priority: 10
        }
      }
    }
  }
};
```

---

## Interview Q&A

### 1. What is the main difference between webpack and Rspack?
**Answer**: Rspack is a Rust-based bundler that provides 5-10x faster bundling compared to webpack. Rspack uses SWC for JavaScript transformation and offers better caching strategies. It's mostly webpack-compatible but can be a drop-in replacement with faster performance.

### 2. Explain the webpack module resolution algorithm.
**Answer**: Webpack resolves modules in this order:
1. Check for absolute path
2. Check node_modules folders
3. Check configured alias
4. Use fallback to extensions (.js, .json, etc.)
5. Use package.json main field

### 3. What are loaders in webpack?
**Answer**: Loaders are transformers that process non-JavaScript files (CSS, images, fonts, etc.) into modules that webpack can understand and bundle. Examples: babel-loader, css-loader, file-loader.

### 4. What's the difference between plugins and loaders?
**Answer**: 
- **Loaders**: Transform specific file types during bundling
- **Plugins**: Extend webpack's functionality by hooking into lifecycle events

### 5. How do you optimize bundle size?
**Answer**:
- Tree shaking: Remove unused code
- Code splitting: Break code into chunks
- Minification: Compress code
- Lazy loading: Load code on demand
- Compression: Use gzip or brotli

### 6. What is tree shaking?
**Answer**: Tree shaking removes unused code from the final bundle by analyzing static imports/exports. Works best with ES6 modules. Enable with `usedExports: true` in optimization.

### 7. Explain code splitting in webpack.
**Answer**: Code splitting breaks your code into multiple chunks that can be loaded on demand or in parallel. Methods include:
- Entry points
- Dynamic imports
- SplitChunksPlugin

### 8. What is Hot Module Replacement (HMR)?
**Answer**: HMR allows modules to be updated in the browser without full page reload. It preserves application state and improves development experience.

### 9. How do you handle CSS in webpack?
**Answer**: Use loaders like style-loader and css-loader:
```javascript
{
  test: /\.css$/,
  use: ['style-loader', 'css-loader']
}
```
For production, use mini-css-extract-plugin to extract CSS into separate files.

### 10. What is the purpose of the output.filename configuration?
**Answer**: Specifies the output filename(s) for generated bundles. Supports templating:
- `[name]`: Entry point name
- `[hash]`: Build hash
- `[contenthash]`: File content hash

### 11. How do you implement code splitting with dynamic imports?
**Answer**:
```javascript
function loadModule() {
  import('./expensive-module').then(module => {
    module.default();
  });
}
```

### 12. What is a manifest file in webpack?
**Answer**: The manifest contains the mapping between module IDs and their corresponding output files. It's used for module resolution at runtime.

### 13. Explain webpack's module concatenation (scope hoisting).
**Answer**: Combines all modules into a single scope when possible, reducing bundle size and improving runtime performance. Enable with `concatenateModules: true`.

### 14. What's the difference between `mode: 'development'` and `mode: 'production'`?
**Answer**:
- **development**: Creates readable code, source maps, faster builds
- **production**: Minifies code, optimizes for size and performance

### 15. How do you exclude node_modules from transpilation?
**Answer**:
```javascript
{
  test: /\.js$/,
  exclude: /node_modules/,
  use: 'babel-loader'
}
```

### 16. What is the SplitChunksPlugin?
**Answer**: Extracts common chunks from multiple entry points or creates separate vendor chunk for node_modules dependencies.

### 17. How do you configure webpack for multiple entry points?
**Answer**:
```javascript
module.exports = {
  entry: {
    app: './src/app.js',
    admin: './src/admin.js'
  },
  output: {
    filename: '[name].js',
    path: __dirname + '/dist'
  }
};
```

### 18. What is the purpose of the resolve.alias configuration?
**Answer**: Creates alias for module imports for shorter/clearer paths:
```javascript
resolve: {
  alias: {
    '@': path.resolve(__dirname, 'src/'),
  }
}
```

### 19. How do you optimize images in webpack?
**Answer**: Use image-webpack-loader or asset modules:
```javascript
{
  test: /\.(png|jpg|jpeg|gif)$/,
  type: 'asset',
  parser: {
    dataUrlCondition: {
      maxSize: 8 * 1024 // 8kb
    }
  }
}
```

### 20. What are source maps?
**Answer**: Maps minified/transpiled code back to original source for debugging. Generated with `devtool` option. Types: eval, source-map, cheap-module-source-map, etc.

### 21. How do you configure webpack for TypeScript?
**Answer**:
```javascript
{
  test: /\.tsx?$/,
  use: 'ts-loader',
  exclude: /node_modules/
}
```

### 22. What's the difference between webpack and Vite?
**Answer**: Webpack bundles all code, Vite serves native ES modules in dev and uses Rollup for production. Vite is faster in development but webpack is more mature with more plugins.

### 23. How do you implement lazy loading in React with webpack?
**Answer**:
```javascript
const Component = lazy(() => import('./Component'));

<Suspense fallback={<Loading />}>
  <Component />
</Suspense>
```

### 24. What is a webpack plugin?
**Answer**: A JavaScript class with an apply method that hooks into webpack's lifecycle. Plugins can modify behavior and emit assets.

### 25. How do you minify JavaScript in webpack?
**Answer**: In production mode, webpack uses TerserPlugin by default. Configure explicitly:
```javascript
optimization: {
  minimize: true,
  minimizer: [new TerserPlugin()]
}
```

### 26. What is the entry point in webpack?
**Answer**: The entry point is the root module where webpack starts building the dependency graph. Default is `./src/index.js`.

### 27. How do you handle assets (images, fonts) in webpack?
**Answer**: Use asset type in webpack 5:
```javascript
{
  test: /\.(png|jpg|gif)$/,
  type: 'asset/resource'
}
```

### 28. What is contextual replacement?
**Answer**: Replacing modules conditionally based on context. Used with ContextReplacementPlugin.

### 29. How do you optimize vendor splits?
**Answer**:
```javascript
splitChunks: {
  cacheGroups: {
    vendor: {
      test: /[\\/]node_modules[\\/]/,
      name: 'vendors',
      priority: 10,
      reuseExistingChunk: true
    }
  }
}
```

### 30. What is webpack-dev-server?
**Answer**: Development server that watches file changes, rebuilds bundles, and serves them via HTTP with HMR support.

### 31. How do you configure environment variables in webpack?
**Answer**:
```javascript
const webpack = require('webpack');

module.exports = {
  plugins: [
    new webpack.DefinePlugin({
      'process.env.NODE_ENV': JSON.stringify('production')
    })
  ]
};
```

### 32. What is the ProvidePlugin?
**Answer**: Makes a module available as variable in all modules without requiring import.

### 33. How do you handle CSS modules in webpack?
**Answer**:
```javascript
{
  test: /\.module\.css$/,
  use: [
    'style-loader',
    {
      loader: 'css-loader',
      options: { modules: true }
    }
  ]
}
```

### 34. What is differential loading?
**Answer**: Serving different code bundles to different browsers based on capabilities. Useful for ES5 vs ES6 support.

### 35. How do you implement caching in webpack?
**Answer**:
```javascript
output: {
  filename: '[name].[contenthash].js',
  chunkFilename: '[id].[contenthash].js'
}
```

### 36. What is module federation?
**Answer**: Feature enabling separate webpack builds to function as single application at runtime, sharing dependencies.

### 37. How do you reduce bundle size with webpack?
**Answer**: Tree shaking, code splitting, minification, lazy loading, compression, removing unused dependencies.

### 38. What's the difference between contenthash and hash in filename?
**Answer**: 
- `hash`: Build hash (changes if any file changes)
- `contenthash`: File content hash (changes only if file content changes)

### 39. How do you handle circular dependencies?
**Answer**: Restructure code to avoid circular dependencies. Use dynamic imports or extract shared code into separate module.

### 40. What is the IgnorePlugin used for?
**Answer**: Excludes modules from being processed during bundling. Useful to exclude locales or optional dependencies.

### 41. How do you configure webpack for production?
**Answer**: Use `mode: 'production'`, enable minification, source maps, code splitting, and asset optimization.

### 42. What is the DllPlugin?
**Answer**: Separates dependencies from application code for faster development builds. Vendor DLL compiled once, reused.

### 43. How do you implement Progressive Web App (PWA) with webpack?
**Answer**: Use WorkboxPlugin or webpack-pwa-manifest to generate service worker and manifest.

### 44. What is the CopyPlugin used for?
**Answer**: Copies files/folders to output directory. Useful for static assets, favicons, etc.

### 45. How do you handle polyfills in webpack?
**Answer**: Include in entry point or use @babel/preset-env for automatic polyfill injection.

### 46. What is the BundleAnalyzerPlugin?
**Answer**: Visual analysis tool showing bundle composition, identifying large modules and opportunities for optimization.

### 47. How do you optimize fonts in webpack?
**Answer**: Use asset/resource type, consider web font formats (woff2), preload critical fonts.

### 48. What is the purpose of the optimization.runtimeChunk?
**Answer**: Extracts webpack runtime code into separate chunk for better caching when using code splitting.

### 49. How do you configure babel with webpack?
**Answer**:
```javascript
{
  test: /\.js$/,
  use: {
    loader: 'babel-loader',
    options: {
      presets: ['@babel/preset-env']
    }
  }
}
```

### 50. What is sideEffects in package.json?
**Answer**: Indicates whether modules have side effects. Used for tree shaking. `"sideEffects": false` marks all modules as pure.

### 51. How do you implement tree shaking?
**Answer**: Use ES6 modules, set `usedExports: true` in optimization, and mark packages as `sideEffects: false`.

### 52. What are externals in webpack?
**Answer**: Prevents bundling of specified modules, assumes they'll be available in runtime. Useful for excluding jQuery, etc.

### 53. How do you handle global variables in webpack?
**Answer**: Use ProvidePlugin or DefinePlugin to make globals available.

### 54. What is the MiniCssExtractPlugin?
**Answer**: Extracts CSS into separate files instead of embedded in JS. Recommended for production.

### 55. How do you implement service workers with webpack?
**Answer**: Use WorkboxWebpackPlugin for automatic service worker generation.

### 56. What is the CommonsChunkPlugin (webpack 3)?
**Answer**: Extracts common chunks from entry points. Replaced by SplitChunksPlugin in webpack 4+.

### 57. How do you configure multiple output paths?
**Answer**: Configure multiple entry points with corresponding output filenames using templates.

### 58. What is the NotarizePlugin?
**Answer**: Webpack doesn't have this. Possibly referring to macOS code signing in build process.

### 59. How do you handle CSS preprocessing (SASS/LESS) in webpack?
**Answer**:
```javascript
{
  test: /\.scss$/,
  use: ['style-loader', 'css-loader', 'sass-loader']
}
```

### 60. What is the InlineChunkHtmlPlugin?
**Answer**: Inlines specific chunks into HTML file, reducing HTTP requests.

### 61. How do you optimize React apps with webpack?
**Answer**: Code splitting at route level, lazy loading, dynamic imports, removing unused dependencies, minification.

### 62. What is the LimitChunkCountPlugin?
**Answer**: Merges chunks when they exceed specified count, useful for preventing too many small chunks.

### 63. How do you implement feature flags with webpack?
**Answer**: Use DefinePlugin with environment variables or create feature flag module.

### 64. What is the CompressionPlugin?
**Answer**: Generates gzip/brotli compressed versions of assets for better compression served by server.

### 65. How do you handle SVG in webpack?
**Answer**: Use svg-loader, svg-inline-loader, or asset type depending on use case.

### 66. What is the optimize-css-assets-webpack-plugin?
**Answer**: Optimizes and minifies CSS assets in webpack builds.

### 67. How do you implement monorepo with webpack?
**Answer**: Use shared webpack configuration, separate entry points for packages, proper path resolution.

### 68. What is the webpack-merge library?
**Answer**: Utility to merge webpack configurations, useful for sharing common config across dev/prod.

### 69. How do you debug webpack configurations?
**Answer**: Use `--debug` flag, inspect output, use webpack-bundle-analyzer, or use Node debugger.

### 70. What is the NormalModuleReplacementPlugin?
**Answer**: Replaces modules with other modules based on conditions. Useful for conditional code loading.

### 71. How do you implement server-side rendering (SSR) with webpack?
**Answer**: Create separate webpack config for server, handle entry differently, exclude server code from client bundle.

### 72. What is the NamedModulesPlugin?
**Answer**: Uses module names instead of IDs for clearer debugging. Now default in development mode.

### 73. How do you handle WASM modules in webpack?
**Answer**: Webpack 5 has built-in WASM support. Use `type: 'webassembly/async'` or `sync`.

### 74. What is the performance hints configuration?
**Answer**: Warns when bundle size exceeds specified threshold. Configure with `performance` option.

### 75. How do you implement partial hydration with webpack?
**Answer**: Separate server and client entry points with selective component hydration.

### 76. What is the watch mode in webpack?
**Answer**: Webpack watches file changes and rebuilds automatically. Enable with `--watch` or `watch: true`.

### 77. How do you optimize dependency resolution?
**Answer**: Configure resolve.alias, use resolve.mainFields, prioritize extensions, avoid extensive resolution chains.

### 78. What is the stats option in webpack?
**Answer**: Controls verbosity of build output. Options: verbose, normal, minimal, errors-only, etc.

### 79. How do you implement dynamic requires with webpack?
**Answer**: Use `require.context()` or dynamic imports with expressions for conditional module loading.

### 80. What is the loader context?
**Answer**: Object passed to loaders containing metadata about module being processed, utilities for caching, etc.

### 81. How do you profile webpack build?
**Answer**: Use `--profile` flag to measure timings, analyze with webpack-bundle-analyzer or speed-measure-webpack-plugin.

### 82. What is the fallback configuration in resolve?
**Answer**: Provides fallback modules when module resolution fails. Useful for polyfilling Node modules in browser.

### 83. How do you implement preloading and prefetching?
**Answer**: Use webpack magic comments or preload/prefetch links in HTML for resource hints.

### 84. What is the CleanWebpackPlugin?
**Answer**: Cleans output directory before build to remove old generated files.

### 85. How do you handle Express.js middleware with webpack?
**Answer**: Use webpack-dev-middleware to integrate webpack with Express server.

### 86. What is the ModuleIdPlugin?
**Answer**: Controls how module IDs are assigned for deterministic builds across different machines.

### 87. How do you implement A/B testing with webpack?
**Answer**: Use feature flags with DefinePlugin or create separate entry points for variants.

### 88. What is the ContextReplacementPlugin used for?
**Answer**: Replaces modules matched by require context expression, controlling bundle size for dynamic requires.

### 89. How do you optimize images with Webpack 5?
**Answer**: Use asset type with parser.dataUrlCondition for automatic inline/resource decision.

### 90. What is the difference between webpack and Parcel?
**Answer**: Webpack requires configuration, Parcel is zero-config. Webpack offers more control, Parcel is easier to use for simple projects.

### 91. How do you implement error boundaries with webpack code splitting?
**Answer**: Wrap lazy-loaded components with error boundary to handle loading failures gracefully.

### 92. What is the GlobEntriesPlugin?
**Answer**: Automatically creates entry points from glob patterns for dynamic entry generation.

### 93. How do you handle CSS-in-JS with webpack?
**Answer**: Libraries like styled-components work natively. Webpack handles JS extraction of styles.

### 94. What is the HardSourceWebpackPlugin?
**Answer**: Provides intermediate caching layer for faster builds by caching modules from previous builds.

### 95. How do you implement buildtime compilation with webpack?
**Answer**: Use loaders and plugins to compile code, templates, or assets at build time.

### 96. What is the difference between webpack and Rollup?
**Answer**: Webpack for applications, Rollup for libraries. Rollup better tree shaking, webpack better module handling.

### 97. How do you optimize webpack for monorepos?
**Answer**: Share configurations, optimize resolution, use workspace packages, implement proper module federation.

### 98. What is the speed-measure-webpack-plugin?
**Answer**: Measures webpack build speed, identifying slow loaders and plugins for optimization.

### 99. How do you implement workspace protocol in webpack?
**Answer**: Use workspace protocol in package.json to reference local packages in monorepo.

### 100. What are the best practices for webpack configuration?
**Answer**:
- Keep configurations DRY using webpack-merge
- Use environment-specific configs (dev/prod)
- Document custom plugins/loaders
- Profile builds regularly
- Keep dependencies updated
- Use caching strategies
- Implement code splitting
- Monitor bundle size
- Use source maps appropriately
- Test configurations in CI/CD

---

## Best Practices Summary

### Development
- Enable source maps for debugging
- Use webpack-dev-server with HMR
- Configure watch mode
- Use fast loaders (esbuild-loader, swc-loader)

### Production
- Enable minification and code splitting
- Use content-based hashing for caching
- Extract critical CSS
- Compress assets (gzip/brotli)
- Analyze bundle with BundleAnalyzerPlugin

### General
- Keep webpack configuration simple
- Document custom configurations
- Use preset configurations when possible
- Monitor build performance
- Implement proper caching strategies
- Use tree shaking
- Keep dependencies minimal

---

## Migration Guides

### Webpack to Vite
- Remove webpack config
- Update build scripts
- Install Vite and plugins
- Update entry points if needed
- Test thoroughly

### Webpack 4 to Webpack 5
- Update CLI to v5
- Update loaders/plugins for compatibility
- Test sideEffects tree shaking
- Verify asset handling
- Test module federation if using

### Webpack to Rspack
- Install Rspack
- Use similar config (mostly compatible)
- Test thoroughly
- May need to update some plugins/loaders
- Leverage faster builds

---

## Common Issues & Solutions

### Bundle Size Increasing
- Analyze bundle with BundleAnalyzerPlugin
- Implement code splitting
- Remove unused dependencies
- Enable tree shaking
- Check for duplicate packages

### Build Time Too Long
- Profile build with speed-measure-webpack-plugin
- Replace slow loaders
- Implement caching
- Use DLL plugin
- Consider Rspack or Vite

### HMR Not Working
- Check webpack-dev-server configuration
- Verify file watching enabled
- Check for compilation errors
- Update packages if outdated
- Test in incognito mode

### Module Resolution Issues
- Check resolve configuration
- Verify extensions and alias
- Check node_modules existence
- Clear build cache
- Check loader exclude patterns

---

## Resources
- [Webpack Official Docs](https://webpack.js.org/)
- [Rspack Official Docs](https://www.rspack.dev/)
- [Vite Official Docs](https://vitejs.dev/)
- [Parcel Official Docs](https://parceljs.org/)
- [esbuild Official Docs](https://esbuild.github.io/)
