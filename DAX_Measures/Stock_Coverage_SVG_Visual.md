# 📊 Dynamic Stock Coverage Visual (SVG Progress Bar)

**Description:**
This measure constructs an interactive, in-cell SVG progress bar that visually represents inventory coverage. It calculates the ratio of Stock on Hand to Total Demand, dynamically adjusting the width of the SVG rectangle (`_FillWidth`). It also features conditional formatting, shifting colors dynamically to highlight shortages, providing an intuitive, ERP-style visual cue directly inside Power BI tables/matrices.

```dax
Stock Visual SVG = 

// 1. Calculate Demand and Stock totals
VAR _Demand = SUM(Demand_FG[Plan])
VAR _Stock = SUM(Demand_FG[Total ( ton )])

// 2. Calculate Coverage Ratio & constrain Max Width percentage to 100% (1)
VAR _Coverage = DIVIDE(_Stock, _Demand, 0)
VAR _DisplayPercent = MIN(_Coverage, 1)

// 3. Calculate absolute shortage
VAR _Shortage =
    IF(
        _Demand > _Stock,
        _Demand - _Stock,
        0
    )

// 4. Define UI Theme Colors
VAR _Primary = "#3A2761"
VAR _Warning = "#8b5cf6"
VAR _Track = "#eef2f7"
VAR _Text = "#64748b"
VAR _Dark = "#0f172a"
VAR _Danger = "#dc2626"

// 5. Dynamic logic for Progress Bar color
VAR _BarColor =
    IF(
        _Coverage >= 1,
        _Primary,
        _Warning
    )

// 6. Define Progress Bar metrics
VAR _MaxWidth = 360
VAR _FillWidth = _DisplayPercent * _MaxWidth

// 7. Render SVG Markup
RETURN
"data:image/svg+xml;utf8,
<svg viewBox='0 0 420 80' xmlns='[http://www.w3.org/2000/svg](http://www.w3.org/2000/svg)'>

    <filter id='shadow'>
        <feDropShadow dx='0' dy='1' stdDeviation='2' flood-opacity='0.05'/>
    </filter>

    <!-- Coverage Text -->
    <text x='10' y='20' font-family='Segoe UI' font-size='15' fill='"&_Text&"'>
        Coverage:
        <tspan fill='"&_Dark&"' font-weight='600'>
            " & ROUND(_Coverage*100,0) & "%
        </tspan>
    </text>

    <!-- Requirement Text -->
    <text x='410' y='20' text-anchor='end' font-family='Segoe UI' font-size='15' fill='"&_Text&"'>
        Req:
        <tspan fill='"&_Dark&"' font-weight='600'>
            " & FORMAT(_Demand,"#,##0.0") & "
        </tspan>
    </text>

    <!-- Background Track -->
    <rect x='10' y='35' width='390' height='10' rx='5' fill='"&_Track&"'/>

    <!-- Filled Progress Bar -->
    <rect x='10' y='35' width='"&_FillWidth&"' height='10' rx='5' fill='"&_BarColor&"'/>

    <!-- Indicator Dot -->
    <circle cx='"&(10+_FillWidth)&"' cy='40' r='6' fill='white' stroke='"&_BarColor&"' stroke-width='2' filter='url(#shadow)'/>

    <!-- Shortage Label -->
    <text x='10' y='67' font-family='Segoe UI' font-size='15' fill='"&_Text&"'>
        Shortage:
    </text>

    <!-- Dynamic Shortage Value / Status -->
    <text x='410' y='67' text-anchor='end' font-family='Segoe UI' font-size='15' font-weight='600' fill='" & IF(_Shortage>0, _Danger, _Primary) & "'>
        " & IF(_Shortage>0, FORMAT(_Shortage,"#,##0.0"), "Covered ✓") & "
    </text>

</svg>"
