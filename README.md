# 📊 Real-Time Survey Analysis Project
## Data Professional Survey Dashboard - Power BI

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

A comprehensive Power BI dashboard analyzing survey data from **data professionals** across the globe. This project transforms raw survey responses into actionable insights through systematic data cleaning, advanced Power Query transformations, and interactive visualizations.

---

## 📋 Table of Contents
- [Project Overview](#project-overview)
- [Data Transformation Pipeline](#data-transformation-pipeline)
- [Dashboard Visualizations](#dashboard-visualizations)
- [Key Insights](#key-insights)
- [Technical Skills Demonstrated](#technical-skills-demonstrated)
- [How to Use](#how-to-use)

---

## 🎯 Project Overview

This dashboard analyzes survey responses from data professionals to uncover:
- **Salary trends** across different roles and programming languages
- **Work-life balance** and salary satisfaction metrics
- **Geographic distribution** of data professionals
- **Programming language preferences** by role
- **Career demographics** (age, gender distribution)

**Dataset Source:** Real-time survey data from data professionals worldwide  
**Survey Participants:** Data Analysts, Data Scientists, Data Engineers, Database Developers, and more

---

## 🔧 Data Transformation Pipeline

### Power Query Editor - Systematic Data Cleaning

All transformations were performed in **Power Query Editor** to ensure clean, reliable data before visualization.

#### **Step 1: Data Understanding & Column Selection**

After loading the raw survey data, I performed initial analysis to:
- Identify relevant columns for analysis
- Remove redundant or empty columns
- Understand data quality issues
- Plan transformation strategy

**Key Columns Selected:**
- `Unique ID` - Survey respondent identifier
- `Q1 - Which Title Best Fits your Current Role?` - Job title
- `Q5 - Favorite Programming Language` - Programming preferences
- `Q3 - Current Yearly Salary` - Salary information (required complex cleaning)
- `Q6 - Work/Life Balance & Salary Satisfaction` - Happiness metrics
- `Q9 - Male/Female?` - Gender distribution
- `Q10 - Current Age` - Age demographics
- `Q11 - Which Country do you live in?` - Geographic location

---

#### **Step 2: Role & Programming Language Cleanup**

**Challenge:** The "Current Role" column contained combined data with delimiters:
```
Example: "Data Analyst (Python, SQL:"
```

**Transformation Applied:**
```m
// Split column using delimiters: "(", ",", ":"
= Table.SplitColumn(Source, "Q1 - Which Title Best Fits your Current Role?", 
    Splitter.SplitTextByAnyDelimiter({"(", ",", ":"}, QuoteStyle.Csv))

// Removed redundant split columns
// Kept primary role classification clean
// Preserved additional options in separate column
```

**Result:** Clean role categories:
- Data Analyst
- Data Scientist
- Data Engineer
- Database Developer
- Student/Looking/None
- Other

---

#### **Step 3: Advanced Salary Column Processing** ⭐

The salary column was the most complex transformation, containing mixed formats:

**Original Data Examples:**
```
"150-200k"
"175k"
"100-125K"
"50k-75k"
```

**Multi-Step Transformation:**

**Step 3.1: Text-to-Number Separation**
```m
// Separate digit components from non-digit text (k, K)
= Table.SplitColumn(#"Previous Step", "Q3 - Current Yearly Salary", 
    Splitter.SplitTextByCharacterTransition(
        (c) => not List.Contains({"0".."9"}, c), 
        (c) => List.Contains({"0".."9"}, c)))
```

**Step 3.2: Smart Mean Calculation** 🧠
```m
// For salary ranges: Calculate midpoint
// For single values: Apply intelligent conversion
// Formula: (Salary1 + Salary2) / 2
// Edge case: (225 + 225) / 2 = 225

// Example transformations:
// "150-200" → Salary1=150, Salary2=200 → Avg=175
// "175" → Salary1=175, Salary2=175 → Avg=175
```

**Step 3.3: Data Type Conversion**
```m
// Convert from Text to Decimal Number
= Table.TransformColumnTypes(#"Previous Step",
    {{"Salary1", type number}, 
     {"Salary2", type number}})
```

**Step 3.4: Custom Column Creation**
```m
// Create Avg_Income column
= Table.AddColumn(#"Previous Step", "Avg_Income", 
    each ([Salary1] + [Salary2]) / 2, 
    type number)

// Change to Decimal type for precision
= Table.TransformColumnTypes(#"Added Custom",
    {{"Avg_Income", type number}})
```

**Final Result:** Clean numeric salary data ready for aggregation and analysis!

---

#### **Step 4: Geographic Data Cleanup**

**Transformation:**
```m
// Cleaned country names by splitting on delimiters
= Table.SplitColumn(Source, "Q11 - Which Country do you live in?", 
    Splitter.SplitTextByDelimiter("(", QuoteStyle.Csv))

// Standardized country names
// Result: United States, United Kingdom, India, Canada, Other
```

---

## 📊 Dashboard Visualizations

The dashboard consists of **9 interactive visualizations** on a single comprehensive page (1280x720):

### **1. Survey Title Header**
- **Visual Type:** Text box
- **Purpose:** Dashboard branding and context
- **Content:** "Data Professional Survey Breakdown"

### **2. Total Survey Respondents Card** 
- **Visual Type:** Card
- **Metric:** `Count of Unique ID`
- **Insight:** Total number of survey participants
- **Value:** Displays minimum unique ID (represents total count)

### **3. Average Age Card**
- **Visual Type:** Card  
- **Metric:** `Sum of Current Age`
- **Insight:** Average age of data professionals surveyed
- **Purpose:** Understanding workforce demographics

### **4. Average Salary by Job Role** ⭐
- **Visual Type:** Horizontal Bar Chart
- **X-Axis:** `Avg_Income` (custom calculated field)
- **Y-Axis:** `Current Role`
- **Series:** Color-coded by role
- **Insight:** Salary comparison across different data roles
- **Key Finding:** Data Scientists and Data Engineers command highest salaries

### **5. Programming Language Popularity by Role**
- **Visual Type:** Clustered Column Chart
- **X-Axis:** `Favorite Programming Language`
- **Y-Axis:** `Count of Respondents`
- **Legend:** `Job Role` (stacked/clustered)
- **Insight:** Which languages are preferred by which roles
- **Key Finding:** Python dominates across all roles, followed by R and SQL

### **6. Geographic Salary Distribution**
- **Visual Type:** Treemap
- **Group:** `Country`
- **Values:** `Sum of Avg_Income`
- **Insight:** Salary concentration by geographic region
- **Visual Impact:** Size represents total salary pool by country

### **7. Work-Life Balance Satisfaction Gauge**
- **Visual Type:** Gauge Chart
- **Metric:** `Work/Life Balance Happiness Score`
- **Range:** Min to Max satisfaction levels
- **Color Coding:** Red (low) → Yellow → Green (high)
- **Insight:** Overall satisfaction with work-life balance

### **8. Salary Satisfaction Gauge**
- **Visual Type:** Gauge Chart
- **Metric:** `Salary Happiness Score`
- **Range:** Min to Max satisfaction levels
- **Color Coding:** Red (low) → Yellow → Green (high)
- **Insight:** How satisfied professionals are with their compensation

### **9. Demographics Breakdown**
- **Visual Type:** Donut Chart
- **Segments:** 
  - `Gender (Male/Female)`
  - `Age Groups`
- **Values:** `Sum of Avg_Income`
- **Insight:** Salary distribution across demographics
- **Purpose:** Diversity and equity analysis

---

## 🔍 Key Insights Delivered

This dashboard enables stakeholders to answer questions like:

✅ **"What's the average salary for a Data Scientist?"**  
✅ **"Which programming language should I learn for my career path?"**  
✅ **"How satisfied are data professionals with their work-life balance?"**  
✅ **"Which countries offer the highest compensation for data roles?"**  
✅ **"What's the age distribution in the data professional workforce?"**  
✅ **"How does salary vary between male and female professionals?"**

---

## 💡 Technical Skills Demonstrated

### **Power Query M Language**
- ✅ Advanced text parsing with multiple delimiters
- ✅ Custom column creation with calculated fields
- ✅ Data type transformations (Text → Number → Decimal)
- ✅ Conditional logic for edge cases
- ✅ Table splitting and merging operations

### **Data Transformation Expertise**
- ✅ Handling messy, real-world survey data
- ✅ Salary range normalization (smart mean calculation)
- ✅ Text cleaning and standardization
- ✅ Column selection and reduction
- ✅ Null value handling

### **Statistical & Analytical Thinking**
- ✅ Mean calculation for salary ranges
- ✅ Aggregation strategies (Sum, Average, Count)
- ✅ Demographic analysis
- ✅ Satisfaction metric interpretation

### **Data Visualization Design**
- ✅ Multi-chart dashboard composition
- ✅ Color-coded KPI cards
- ✅ Interactive gauge charts for satisfaction metrics
- ✅ Geographic visualizations (treemap)
- ✅ Comparative bar and column charts
- ✅ Demographic breakdowns (donut chart)

### **Business Intelligence Principles**
- ✅ Dashboard storytelling and flow
- ✅ Single-page comprehensive overview
- ✅ Audience-appropriate visualization selection
- ✅ Key metric prioritization (cards at top)

---

## 🚀 How to Use

### **Prerequisites**
- **Power BI Desktop** (Free download from Microsoft)
- Windows 10/11 or compatible system

### **Opening the Dashboard**

1. **Download the file:**
   ```
   Real_Time_Survey_Analysis_Project.pbix
   ```

2. **Open in Power BI Desktop:**
   - Double-click the `.pbix` file, OR
   - Open Power BI Desktop → File → Open → Select file

3. **Refresh Data (Optional):**
   - If connected to live data source: `Home → Refresh`
   - Current version uses embedded static dataset

4. **Explore Interactively:**
   - Click on any visual to cross-filter others
   - Hover over data points for detailed tooltips
   - Use slicers/filters if added

### **Viewing Transformations**

To see the Power Query transformations:

1. Click **Transform Data** in the Home ribbon
2. Navigate to **Applied Steps** panel (right side)
3. Click each step to see the transformation logic
4. View **Advanced Editor** for full M code

### **Customization Options**

- **Update Data Source:** Transform Data → Data Source Settings
- **Modify Visuals:** Click visual → Format panel (paintbrush icon)
- **Add New Pages:** Click "+" at bottom
- **Change Theme:** View → Themes

---

## 📁 Project Structure

```
Real_Time_Survey_Analysis_Project.pbix
├── Data Model
│   └── Data Professional Survey (Table)
│       ├── Unique ID
│       ├── Q1 - Current Role (cleaned)
│       ├── Q5 - Programming Language (cleaned)
│       ├── Salary1 (extracted)
│       ├── Salary2 (extracted)
│       ├── Avg_Income (calculated) ⭐
│       ├── Q6 - Work/Life Balance
│       ├── Q6 - Salary Satisfaction
│       ├── Q9 - Gender
│       ├── Q10 - Age
│       └── Q11 - Country (cleaned)
│
└── Report Pages
    └── Page 1 (Main Dashboard)
        ├── Title Header
        ├── Survey Count Card
        ├── Average Age Card
        ├── Salary by Role Chart
        ├── Programming Language Chart
        ├── Geographic Treemap
        ├── Work-Life Balance Gauge
        ├── Salary Satisfaction Gauge
        └── Demographics Donut Chart
```

---

## 🎓 Learning Outcomes

This project demonstrates mastery of:

1. **ETL Processes:** Extract → Transform → Load workflow
2. **Data Quality Management:** Handling real-world messy data
3. **Business Intelligence:** Translating data into insights
4. **Power BI Ecosystem:** End-to-end dashboard development
5. **Statistical Analysis:** Mean calculations, aggregations
6. **Visual Communication:** Designing for different audiences

---

## 📈 Future Enhancements

Potential improvements for this dashboard:

- [ ] Add date/time analysis (if survey timestamps available)
- [ ] Create drill-through pages for detailed role analysis
- [ ] Implement slicers for country and role filtering
- [ ] Add trend analysis if longitudinal data available
- [ ] Create mobile-optimized layout
- [ ] Add tooltips with additional context
- [ ] Include DAX measures for advanced calculations
- [ ] Implement bookmarks for different views

---

## 👨‍💻 Author

**Data Analyst / BI Developer**

*Skills Applied:* Power BI | Power Query | Data Transformation | DAX | Data Visualization | Statistical Analysis

---

## 📄 License

This project is available for educational and portfolio purposes.

---

## 🙏 Acknowledgments

- Survey data from real data professionals
- Power BI community for best practices
- Microsoft Power BI documentation

---

**Dashboard Created:** 2026  
**Tools Used:** Power BI Desktop, Power Query Editor  
**Report Size:** 1280 x 720 (Standard HD)

---

### 📌 Quick Stats

| Metric | Value |
|--------|-------|
| **Total Visuals** | 9 |
| **Dashboard Pages** | 1 |
| **Data Sources** | 1 (Survey CSV) |
| **Calculated Columns** | 3+ |
| **Transformation Steps** | 15+ |
| **Visual Types** | 6 (Card, Bar, Column, Treemap, Gauge, Donut) |

---
