# futprintv2
Futprint v2.0 - Complete Design & Architecture Document
📋 Executive Summary
Futprint v2.0 is a complete redesign of the Fashion Carbon Calculator https://github.com/sweehan/futprint.git, transforming it into a modular, data-driven platform that can calculate carbon footprints across multiple product categories. This document captures all design decisions, architecture choices, and implementation strategies for creating a fully extensible system where new categories can be added without code modifications.
Key Design Decisions Made:

Modular Plugin Architecture: Each product category (fashion, skincare, makeup) is a self-contained module
Complete Data Separation: All calculations, materials, and UI configurations stored in external data files
CSV/XLS Import: Custom product data can be imported for skincare calculations
Optional Water Usage: Users can choose to include/exclude water usage in lifecycle calculations
API Integration: Built-in support for external carbon data APIs
No User Data Migration: Starting fresh as this is an MVP

🏗️ System Architecture Overview
Core Architecture Principles

Data-Driven Design: Everything configurable through data files
Plugin-Based Modules: Drop-in category support
API-First Integration: External data source connectivity
Progressive Enhancement: Works at multiple capability levels
Complete Style Separation: No inline styles anywhere

High-Level Architecture
┌─────────────────────────────────────────────────────────────┐
│                      User Interface Layer                     │
├─────────────────────────────────────────────────────────────┤
│  Core Framework  │  Module Loader  │  UI Component Library   │
├─────────────────────────────────────────────────────────────┤
│              Calculation Engine & Business Logic              │
├─────────────────────────────────────────────────────────────┤
│   Data Layer   │   API Gateway   │   Storage Abstraction    │
├─────────────────────────────────────────────────────────────┤
│  External APIs  │  Local Storage  │  CSV/XLS Import Engine   │
└─────────────────────────────────────────────────────────────┘
Detailed File Structure
/futprint-v2/
├── /core/
│   ├── app.js                    # Main application initialization
│   ├── router.js                 # Category navigation handler
│   ├── module-loader.js          # Dynamic module loading system
│   ├── calculation-engine.js     # Generic calculation framework
│   ├── api-gateway.js            # External API integration layer
│   ├── data-import.js            # CSV/XLS import functionality
│   └── ui-framework.js           # Base UI component system
│
├── /modules/                     # Category-specific modules
│   ├── /fashion/
│   │   ├── fashion.module.js    # Fashion calculator logic
│   │   ├── fashion.wizard.js    # Fashion-specific UI flow
│   │   └── fashion.styles.css   # Fashion module styles
│   │
│   ├── /skincare/
│   │   ├── skincare.module.js   # Skincare calculator logic
│   │   ├── skincare.wizard.js   # Skincare UI flow
│   │   ├── skincare.import.js   # Custom product import
│   │   └── skincare.styles.css  # Skincare module styles
│   │
│   └── /makeup/                  # Future makeup module
│
├── /data/
│   ├── /categories/
│   │   ├── /fashion/
│   │   │   ├── config.json      # Fashion category config
│   │   │   ├── materials.json   # Fabric carbon data
│   │   │   └── products.json    # Fashion product templates
│   │   │
│   │   └── /skincare/
│   │       ├── config.json      # Skincare category config
│   │       ├── ingredients.json # Ingredient carbon data
│   │       ├── packaging.json   # Packaging carbon data
│   │       ├── products.json    # Skincare product templates
│   │       └── /custom/         # User-imported products
│   │           └── *.csv        # Custom product data files
│   │
│   ├── /shared/
│   │   ├── locations.json       # Manufacturing locations
│   │   ├── transportation.json # Shipping carbon factors
│   │   └── water-usage.json    # Optional water calculations
│   │
│   └── /api-mappings/
│       ├── carbon-minds.json    # Carbon Minds API mapping
│       ├── devera.json          # Devera API mapping
│       └── openlca.json         # OpenLCA API mapping
│
├── /styles/
│   ├── /core/
│   │   ├── reset.css           # CSS reset/normalize
│   │   ├── variables.css       # CSS custom properties
│   │   ├── components.css      # Base component styles
│   │   └── layout.css          # Layout system
│   │
│   ├── /themes/
│   │   ├── sage-green.css      # Current theme
│   │   ├── ocean-blue.css      # Alternative theme
│   │   └── earth-brown.css     # Alternative theme
│   │
│   └── /modules/               # Module-specific styles
│
├── /plugins/
│   ├── gamification/
│   │   ├── gamification.js     # Points & achievements
│   │   └── achievements.json   # Achievement definitions
│   │
│   ├── sharing/
│   │   ├── share.js           # Social sharing
│   │   └── templates.json     # Share message templates
│   │
│   └── visualization/
│       ├── charts.js          # Data visualization
│       └── comparisons.js     # Alternative comparisons
│
├── /public/
│   ├── index.html             # Main entry point
│   ├── manifest.json          # PWA manifest
│   └── service-worker.js      # Offline functionality
│
└── /tools/
    ├── data-validator.js      # Validate data file formats
    ├── module-generator.js    # Generate new category modules
    └── csv-converter.js       # Convert CSV to internal format
    Data Structure Specifications
1. Category Configuration Schema
   // /data/categories/{category-id}/config.json
{
  "id": "skincare",
  "name": "Skincare & Beauty",
  "version": "1.0.0",
  "icon": "💄",
  "description": "Calculate carbon footprint of skincare and makeup products",
  "theme": {
    "primaryColor": "#FFB6C1",
    "secondaryColor": "#FFF0F5",
    "accentColor": "#FF69B4"
  },
  "units": {
    "volume": ["ml", "oz", "g"],
    "packaging": ["plastic", "glass", "aluminum", "paper", "refill"]
  },
  "calculationOptions": {
    "includeWaterUsage": {
      "default": false,
      "userConfigurable": true,
      "label": "Include water usage in calculation"
    },
    "lifecyclePhases": ["materials", "manufacturing", "packaging", "transport", "usage", "disposal"]
  },
  "wizardSteps": [
    {
      "id": "product-type",
      "title": "Select Product Type",
      "component": "ProductSelector"
    },
    {
      "id": "ingredients",
      "title": "Ingredients & Formula",
      "component": "IngredientMixer"
    },
    {
      "id": "packaging",
      "title": "Packaging Details",
      "component": "PackagingSelector"
    },
    {
      "id": "usage",
      "title": "Usage Pattern",
      "component": "UsageCalculator",
      "optional": true
    }
  ],
  "dataSources": {
    "internal": ["ingredients.json", "packaging.json"],
    "external": ["carbon-minds", "devera"],
    "userImport": {
      "enabled": true,
      "formats": ["csv", "xlsx"],
      "template": "skincare-import-template.csv"
    }
  }
}
2. Skincare Ingredients Database Structure
// /data/categories/skincare/ingredients.json
{
  "metadata": {
    "lastUpdated": "2024-12-20",
    "sources": ["EPA", "Carbon Minds", "Academic Research"],
    "version": "1.0.0"
  },
  "ingredients": {
    "water": {
      "inci": "Aqua",
      "category": "solvent",
      "carbonData": {
        "extraction": 0.001,
        "processing": 0.002,
        "total_kg_co2_per_kg": 0.003
      },
      "waterUsage": {
        "liters_per_kg": 1.2
      },
      "source": "EPA Water Treatment Data 2023"
    },
    "glycerin": {
      "inci": "Glycerin",
      "category": "humectant",
      "carbonData": {
        "synthetic": {
          "extraction": 1.2,
          "processing": 1.41,
          "total_kg_co2_per_kg": 2.61
        },
        "vegetable": {
          "extraction": 0.8,
          "processing": 0.9,
          "total_kg_co2_per_kg": 1.7
        }
      },
      "defaultType": "vegetable",
      "source": "Chemical Industry Report 2024"
    },
    "retinol": {
      "inci": "Retinol",
      "category": "active",
      "carbonData": {
        "extraction": 15.2,
        "processing": 23.8,
        "total_kg_co2_per_kg": 39.0
      },
      "typicalConcentration": "0.1-1%",
      "source": "Pharmaceutical LCA Database"
    }
  },
  "categories": {
    "solvents": ["water", "alcohol", "propylene-glycol"],
    "emollients": ["oils", "butters", "esters"],
    "actives": ["retinol", "vitamin-c", "niacinamide"],
    "preservatives": ["phenoxyethanol", "sodium-benzoate"]
  }
}
3. CSV Import Template for Custom Products
   # skincare-import-template.csv
Product Name,Product Type,Size (ml),Ingredient 1,% 1,Ingredient 2,% 2,...,Packaging Type,Packaging Weight (g)
"Custom Face Cream","moisturizer",50,"water",70,"glycerin",10,...,"glass jar",120
"My Serum","serum",30,"water",60,"hyaluronic-acid",2,...,"glass dropper",80
4. API Integration Configuration
// /data/api-mappings/carbon-minds.json
{
  "api": {
    "name": "Carbon Minds cm.chemicals",
    "baseUrl": "https://api.carbon-minds.com/v1",
    "authentication": "bearer",
    "rateLimit": "100/hour"
  },
  "mappings": {
    "ingredients": {
      "endpoint": "/chemicals/search",
      "parameters": {
        "name": "{{ingredient.inci}}",
        "region": "{{location.code}}"
      },
      "response": {
        "carbonFootprint": "data.pcf.total",
        "unit": "data.pcf.unit",
        "lifecycle": "data.lca"
      }
    }
  },
  "cache": {
    "enabled": true,
    "duration": "7d"
  }
}
Module System Design
Base Module Interface
// /core/module.interface.js
export class CategoryModule {
  constructor(config, dataLoader, calculator) {
    this.config = config;
    this.dataLoader = dataLoader;
    this.calculator = calculator;
    this.state = {};
  }

  // Lifecycle methods
  async initialize() {}
  async loadData() {}
  async loadCustomData(file) {} // For CSV/XLS import
  
  // UI methods
  renderWizard(container) {}
  updateStep(stepId, data) {}
  
  // Calculation methods
  async calculate(inputs) {}
  async calculateWithAPIs(inputs) {}
  
  // Optional features
  getAlternatives(result) {}
  getVisualizations(result) {}
  exportResults(format) {}
}
Skincare Module Implementation
// /modules/skincare/skincare.module.js
export class SkincareModule extends CategoryModule {
  async loadCustomData(file) {
    // Handle CSV/XLS import
    const data = await this.parseFile(file);
    const validated = this.validateCustomProduct(data);
    await this.dataLoader.saveCustomProduct(validated);
  }

  async calculate(inputs) {
    const { ingredients, packaging, usage } = inputs;
    
    // Phase 1: Material extraction
    const materialImpact = await this.calculateIngredients(ingredients);
    
    // Phase 2: Manufacturing
    const manufacturingImpact = this.calculateManufacturing(inputs);
    
    // Phase 3: Packaging
    const packagingImpact = this.calculatePackaging(packaging);
    
    // Phase 4: Transportation
    const transportImpact = this.calculateTransport(inputs);
    
    // Phase 5: Usage (optional)
    let usageImpact = 0;
    if (this.config.calculationOptions.includeWaterUsage.value) {
      usageImpact = this.calculateWaterUsage(usage);
    }
    
    // Phase 6: End of life
    const disposalImpact = this.calculateDisposal(packaging);
    
    return {
      total: materialImpact + manufacturingImpact + packagingImpact + 
             transportImpact + usageImpact + disposalImpact,
      breakdown: {
        materials: materialImpact,
        manufacturing: manufacturingImpact,
        packaging: packagingImpact,
        transport: transportImpact,
        usage: usageImpact,
        disposal: disposalImpact
      }
    };
  }
}
API Integration Layer
API Gateway Design
// /core/api-gateway.js
export class APIGateway {
  constructor() {
    this.providers = new Map();
    this.cache = new Cache();
  }

  registerProvider(name, config) {
    this.providers.set(name, new APIProvider(config));
  }

  async fetchIngredientData(ingredient, providers = ['carbon-minds']) {
    const results = [];
    
    for (const provider of providers) {
      try {
        const cached = await this.cache.get(`${provider}:${ingredient}`);
        if (cached) {
          results.push(cached);
          continue;
        }
        
        const data = await this.providers.get(provider).fetchIngredient(ingredient);
        await this.cache.set(`${provider}:${ingredient}`, data);
        results.push(data);
      } catch (error) {
        console.error(`Failed to fetch from ${provider}:`, error);
      }
    }
    
    return this.mergeResults(results);
  }
}
Available Data Sources
1. Carbon Minds (cm.chemicals)

Coverage: 1,500+ chemicals, 200+ regions
Strengths: Chemical-specific data, regional variations
API: Available with subscription
Use for: Synthetic ingredients, preservatives

2. Devera

Coverage: Cosmetic-specific LCA
Strengths: AI-powered, fast calculations
API: Available
Use for: Complete product assessments

3. OpenLCA Nexus

Coverage: Open database
Strengths: Free, community-driven
API: REST API available
Use for: Basic ingredients, packaging

4. Ecoinvent

Coverage: Comprehensive LCA database
Strengths: Academic rigor
API: Through partners
Use for: Manufacturing processes

5. EPA Databases

Coverage: Water, energy, emissions
Strengths: Government data
API: Public access
Use for: Utilities, water usage

💡 Features & Implementation Details
1. CSV/XLS Import System
// /core/data-import.js
export class DataImporter {
  constructor() {
    this.parsers = {
      csv: new CSVParser(),
      xlsx: new XLSXParser()
    };
  }

  async importProducts(file, category) {
    const extension = file.name.split('.').pop();
    const parser = this.parsers[extension];
    
    const data = await parser.parse(file);
    const validated = await this.validateData(data, category);
    
    return this.saveToCustomProducts(validated, category);
  }

  validateData(data, category) {
    // Validate against category schema
    // Map ingredient names to INCI
    // Verify percentages sum to 100
    // Check packaging types
  }
}
2. Optional Water Usage Calculation
// /modules/skincare/water-calculator.js
export class WaterUsageCalculator {
  constructor(config) {
    this.enabled = config.includeWaterUsage;
    this.factors = {
      'face-wash': { litersPerUse: 2, heatingEnergy: 0.1 },
      'shower-product': { litersPerUse: 10, heatingEnergy: 0.5 },
      'leave-on': { litersPerUse: 0.5, heatingEnergy: 0.02 }
    };
  }

  calculate(product, usagePattern) {
    if (!this.enabled) return 0;
    
    const waterPerUse = this.factors[product.type].litersPerUse;
    const heatingPerUse = this.factors[product.type].heatingEnergy;
    const totalUses = this.calculateTotalUses(product, usagePattern);
    
    return {
      waterImpact: waterPerUse * totalUses * 0.001, // kg CO2
      heatingImpact: heatingPerUse * totalUses * 0.233 // kg CO2
    };
  }
}
3. Gamification Enhancements
// /plugins/gamification/achievements.json
{
  "achievements": {
    "skincare_starter": {
      "name": "Clean Beauty Beginner",
      "criteria": "Complete first skincare calculation",
      "points": 25,
      "icon": "🧴"
    },
    "ingredient_investigator": {
      "name": "Ingredient Investigator",
      "criteria": "Import custom product via CSV",
      "points": 50,
      "icon": "🔬"
    },
    "water_warrior": {
      "name": "Water Conservation Hero",
      "criteria": "Calculate 10 products with water usage",
      "points": 100,
      "icon": "💧"
    },
    "api_explorer": {
      "name": "Data Explorer",
      "criteria": "Use 3 different API sources",
      "points": 75,
      "icon": "🌐"
    }
  }
}
Ideas Considered but Not Implemented
1. Blockchain Integration

Idea: Store carbon certificates on blockchain
Reason Discarded: Over-engineering for MVP, adds complexity without clear user value

2. AI Ingredient Suggestions

Idea: ML model to suggest eco-friendly alternatives
Reason Discarded: Requires extensive training data, better suited for v3

3. Real-time Collaboration

Idea: Multiple users working on same calculation
Reason Discarded: No user accounts in MVP, adds infrastructure complexity

4. Native Mobile Apps

Idea: iOS/Android apps instead of PWA
Reason Discarded: PWA provides sufficient functionality, easier maintenance

5. Built-in E-commerce

Idea: Sell eco-friendly products directly
Reason Discarded: Scope creep, changes app focus from calculator to marketplace

🔧 Technical Implementation Notes
Performance Optimizations

Lazy Loading: Load category modules only when accessed
API Caching: 7-day cache for external API calls
Service Worker: Aggressive caching for offline use
Code Splitting: Separate bundles per category

Security Considerations

API Keys: Store in environment variables, never in code
CORS: Whitelist only required API domains
Input Validation: Sanitize all CSV imports
Rate Limiting: Implement client-side API rate limiting

Browser Support

Modern Browsers: Full functionality
IE11: Basic calculator only (no API, no imports)
Mobile: Touch-optimized, reduced animations

📊 Success Metrics

Performance Metrics

Page load time < 2s
Calculation time < 500ms (without API)
API response time < 3s


User Engagement

Category adoption rate
CSV import usage
API integration usage
Achievement completion rate


Data Quality

Calculation accuracy vs. industry standards
API data freshness
Custom product validation success rate



🚀 Implementation Phases
Phase 1: Core Framework (2 weeks)

Module system architecture
Data loading infrastructure
Base UI components
CSV/XLS import engine

Phase 2: Skincare Module (2 weeks)

Ingredient database compilation
Skincare calculation logic
Custom product import
Water usage options

Phase 3: API Integration (1 week)

API gateway implementation
Provider configurations
Caching system
Rate limiting

Phase 4: Testing & Optimization (1 week)

Cross-browser testing
Performance optimization
Data validation
User acceptance testing

🎯 Key Context for Implementation
For AI Assistant/Developer:

Current State: Version 1 exists with fashion calculator only
Priority: Skincare module with CSV import is top priority
Architecture: Must be completely modular - no hardcoding
Data Sources: Use Carbon Minds and Devera APIs primarily
User Data: No migration needed, starting fresh
Water Calculation: Must be optional toggle for users
Styling: All styles external, no inline CSS
Standards: Follow existing patterns from v1 where applicable

Critical Success Factors:

True Modularity: Adding new categories requires zero code changes
Data Accuracy: Use best available carbon data sources
User Control: Allow custom products via CSV import
Performance: Must work offline after initial load
Maintainability: Clear separation of concerns throughout
Appendix: Sample Data Files
Sample Skincare Product CSV
Product Name,Type,Size,Water %,Glycerin %,Retinol %,Packaging,Weight
"Anti-Aging Cream",moisturizer,50,65,10,0.5,glass-jar,150
"Gentle Cleanser",cleanser,150,80,5,0,plastic-bottle,50
Sample API Response
{
  "ingredient": "glycerin",
  "carbon_footprint": {
    "value": 2.61,
    "unit": "kg CO2e/kg",
    "breakdown": {
      "raw_materials": 1.2,
      "manufacturing": 1.41
    }
  },
  "source": "Carbon Minds Database",
  "last_updated": "2024-12-15"
}
#This document represents the complete v2.0 design as discussed. Any implementation should follow these specifications to ensure consistency and maintainability.
