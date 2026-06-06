# 🎨 Dynamic SVG Info Card (Custom UI)

**Description:**
This measure generates a highly customizable, modern SVG card directly within Power BI. It dynamically reads the selected Finished Good and its corresponding Material, adjusting the UI (pills, truncated text, drop shadows) based on the filter context. This eliminates the need for static images and elevates the dashboard to an ERP-like experience.

```dax
Item Info Card SVG = 
// 1. Retrieve Product Info from filter context
VAR _ItemName = SELECTEDVALUE('Items_D'[Item Description], "Please select item")
VAR _ItemCode = SELECTEDVALUE('Items_D'[Item], "---")

// 2. Retrieve Material Code
VAR _MatCode = SELECTEDVALUE('Materials_D'[Material Code])

// 3. Truncate long product names to prevent UI breaking
VAR _TruncatedName =
    IF(
        LEN(_ItemName) > 60,
        LEFT(_ItemName, 57) & "...",
        _ItemName
    )

// 4. Conditional Material Badge (Renders only if a material is selected)
VAR _MatBadgeSVG =
    IF(
        ISBLANK(_MatCode),
        "",
        "<rect x='310' y='24' width='180' height='32' rx='16' fill='#f8fafc'/>
         <circle cx='330' cy='40' r='4' fill='#60a5fa'/>
         <text x='345' y='45' font-family='Segoe UI' font-size='13' fill='#64748b'>
            MAT: <tspan fill='#1e293b' font-weight='600'>" & _MatCode & "</tspan>
         </text>"
    )

// 5. Construct the final SVG XML with Drop Shadows and modern accents
RETURN
"data:image/svg+xml;utf8,
<svg xmlns='[http://www.w3.org/2000/svg](http://www.w3.org/2000/svg)' viewBox='0 0 700 120'>
    <filter id='shadow'>
        <feDropShadow dx='0' dy='2' stdDeviation='4' flood-opacity='0.08'/>
    </filter>

    <rect x='10' y='10' width='680' height='100' rx='20' fill='white' filter='url(#shadow)'/>

    <rect x='20' y='30' width='4' height='60' rx='4' fill='#3A2761'/>

    <rect x='45' y='24' width='220' height='32' rx='16' fill='#f8fafc'/>
    <circle cx='65' cy='40' r='4' fill='#3A2761'/>
    <text x='80' y='45' font-family='Segoe UI' font-size='13' fill='#64748b'>
        ITEM: <tspan fill='#1e293b' font-weight='600'>" & _ItemCode & "</tspan>
    </text>

    " & _MatBadgeSVG & "

    <text x='45' y='85' font-family='Segoe UI' font-size='24' font-weight='600' fill='#0f172a'>
        " & _TruncatedName & "
    </text>
</svg>"
