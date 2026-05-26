# SQL Field List Formatter
![Extension Icon](https://raw.githubusercontent.com/sethwaltersfis/field_list_formatter/refs/heads/main/icon.png)

A SQL-aware Visual Studio Code extension created to format cluttered, single-line, or poorly organized SQL selection lists into (arguably better looking) predictable structures.

Whether you need fields cleanly aligned in multi-column grids or isolated into structured, single-column lists, this extension handles commas, multi-line comments, and intricate alias variations.

## Key Features

**Smart Alias Normalization**
  - Automatically detects implicit field aliases (e.g., `Column AliasName`) and updates them to use an explicit `AS` keyword.
    
**Dynamic Column Layouts**
  - Instantly snap text into fixed column counts (from 2 to 9 columns) or adapt automatically to a custom user-defined default.

**Smarter Grid Alignment**
  - Vertically aligns the `AS` keyword within an output grid column *only* if that column contains two or more aliased items, maximizing horizontal whitespace utilization.

**List Layout Mode**
  - Convert multi-column layouts into an intuitive one-field-per-line style, with complete vertical alignment across the entire query segment.

**Flexible Comma Grammar**
  - Complete architectural support for both traditional trailing commas and modern, highly legible leading commas (with first-row padding alignment).

**Syntax-Aware Tokenizer**
  - Safely processes strings containing commas, brackets, single/double quotes, and inline or block comments without corrupting expressions.

## Installation

### Installing the Prebuilt `.vsix`

1. Download the current `sql-field-list-formatter` .vsix file from the repository releases.
2. Launch Visual Studio Code.
3. Open the Command Palette (`Ctrl+Shift+P` or `Cmd+Shift+P`) and select **Extensions: Install from VSIX...**.
4. Navigate to and select the downloaded `.vsix` file to finish installation.

## Commands & Context Menu Integration

This extension contributes actions to the **Editor Context Menu** (Right-Click) and exposes layout commands via keyboard shortcuts:

| Command Title | Identifier / Function | Default Shortcut |
| :--- | :--- | :--- |
| **Field List Formatter: Format Into Configured Columns** | `fieldListFormatter.formatIntoConfiguredColumns` | *Context Menu Only* |
| **Field List Formatter: Format As List** | `fieldListFormatter.formatAsList` | *Context Menu Only* |
| **Field List Formatter: Prompt For Column Count** | `fieldListFormatter.promptForColumns` | *Context Menu Only* |
| **Field List Formatter: Format Into 2 Columns** | `fieldListFormatter.formatInto2Columns` | `Ctrl+Alt+2` / `Cmd+Alt+2` |
| **Field List Formatter: Format Into 3 Columns** | `fieldListFormatter.formatInto3Columns` | `Ctrl+Alt+3` / `Cmd+Alt+3` |
| **Field List Formatter: Format Into 5 Columns** | `fieldListFormatter.formatIntoFiveColumns` | `Ctrl+Alt+5` / `Cmd+Alt+5` |
| **Field List Formatter: Format Into 4 to 9 Columns** | `fieldListFormatter.formatInto[N]Columns` | `Ctrl+Alt+[N]` / `Cmd+Alt+[N]` |

## Configuration Settings

Customize formatting behaviors in your global or workspace `settings.json`:

### `fieldListFormatter.layoutMode`
- `"grid"` (Default): Distributes fields into horizontally packed columns.
- `"list"`: Forces fields onto their own distinct lines (effectively 1 column).

### `fieldListFormatter.aliasFormat`
- `"align"` (Default): Automatically upgrades implicit aliases to use explicit `AS`, and vertically aligns the `AS` keyword inside any column where at least two items contain an alias.
- `"normalize"`: Upgrades implicit aliases to explicit `AS` using exactly the defined spacing rules, without structural tabular padding.
- `"preserve"`: Leaves original formatting, spacing, and phrasing for aliases exactly as authored in the selection.

### `fieldListFormatter.commaStyle`
- `"leading"` (Default): Formats fields with standard prefix syntax (e.g., `, field_name`).
- `"trailing"`: Appends commas to the end of your expressions (e.g., `field_name,`).

### Additional Fine-Tuning

| Setting Key | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `fieldListFormatter.columns` | `number` | `5` | Fallback column target count for the configuration command. Minimum is `1`. |
| `fieldListFormatter.firstLinePrefixMode` | `string` | `"pad"` | For leading commas: `"pad"` injects two spaces on line 1 for grid alignment; `"commaSpace"` forces a starting comma; `"none"` leaves line 1 un-prefixed. |
| `fieldListFormatter.aliasSpacingBeforeAs` | `number` | `1` | Minimum spaces to maintain between a column field expression and its `AS` token. |
| `fieldListFormatter.aliasSpacingAfterAs` | `number` | `1` | Minimum spaces to maintain between the `AS` token and its trailing alias identifier. |
| `fieldListFormatter.preserveIndentation` | `boolean` | `false` | Finds the shallowest visual indentation layout from your selection and scales all generated blocks from that structural base. |
| `fieldListFormatter.preserveBlankLines` | `boolean` | `false` | Ensures empty lines interspersed throughout long selections are preserved post-format. |

## Alias Formatting Examples

**Input Fragment:**
```sql
x.FIRST_REP_ID
x.FIRST_REP_NAME
x.SECOND_REP_ID AS Sec_Sales_Rep
x.SECOND_REP_NAME
x.SOLUTION_REP_NAME
x.BUSINESS_REP_ID
x.ACCOUNT_MGR_ID AS Account_Mgr
x.SPECIALIST_1
x.SPECIALIST_2
x.OWNER_ID AS Own_ID
x.LAST_OWNER_ID
x.CREATED_BY Created_By_Name
x.FINAL_CHECK_BY
x.LAST_HUMAN_MODIFIED_ID
x.ORIGINAL_LEAD_ID
x.RELATED_CONTACT_ID
x.CONSULTANT_ID
```

### 1. Preserved Multi-Column Grid Mode (`"aliasFormat": "preserve"`)

**Output**
```sql
  x.FIRST_REP_ID      , x.FIRST_REP_NAME               , x.SECOND_REP_ID AS Sec_Sales_Rep, x.SECOND_REP_NAME       , x.SOLUTION_REP_NAME
, x.BUSINESS_REP_ID   , x.ACCOUNT_MGR_ID AS Account_Mgr, x.SPECIALIST_1                  , x.SPECIALIST_2          , x.OWNER_ID AS Own_ID
, x.LAST_OWNER_ID     , x.CREATED_BY Created_By_Name   , x.FINAL_CHECK_BY                , x.LAST_HUMAN_MODIFIED_ID, x.ORIGINAL_LEAD_ID
, x.RELATED_CONTACT_ID, x.CONSULTANT_ID
```

### 2. Normalized Multi-Column Grid Mode (`"aliasFormat": "normalize"`)

**Output**
```sql
  x.FIRST_REP_ID      , x.FIRST_REP_NAME               , x.SECOND_REP_ID AS Sec_Sales_Rep, x.SECOND_REP_NAME       , x.SOLUTION_REP_NAME
, x.BUSINESS_REP_ID   , x.ACCOUNT_MGR_ID AS Account_Mgr, x.SPECIALIST_1                  , x.SPECIALIST_2          , x.OWNER_ID AS Own_ID
, x.LAST_OWNER_ID     , x.CREATED_BY AS Created_By_Name, x.FINAL_CHECK_BY                , x.LAST_HUMAN_MODIFIED_ID, x.ORIGINAL_LEAD_ID
, x.RELATED_CONTACT_ID, x.CONSULTANT_ID
```

### 3. Aligned Multi-Column Grid Mode (`"aliasFormat": "align"`)

**Output**
```sql
  x.FIRST_REP_ID      , x.FIRST_REP_NAME                   , x.SECOND_REP_ID AS Sec_Sales_Rep, x.SECOND_REP_NAME       , x.SOLUTION_REP_NAME
, x.BUSINESS_REP_ID   , x.ACCOUNT_MGR_ID AS Account_Mgr    , x.SPECIALIST_1                  , x.SPECIALIST_2          , x.OWNER_ID AS Own_ID
, x.LAST_OWNER_ID     , x.CREATED_BY     AS Created_By_Name, x.FINAL_CHECK_BY                , x.LAST_HUMAN_MODIFIED_ID, x.ORIGINAL_LEAD_ID
, x.RELATED_CONTACT_ID, x.CONSULTANT_ID
```

## Layout Mode Formatting Examples

**Input Fragment:**
```sql
  x.FIRST_REP_ID      , x.FIRST_REP_NAME               , x.SECOND_REP_ID AS Sec_Sales_Rep, x.SECOND_REP_NAME       , x.SOLUTION_REP_NAME
, x.BUSINESS_REP_ID   , x.ACCOUNT_MGR_ID AS Account_Mgr, x.SPECIALIST_1                  , x.SPECIALIST_2          , x.OWNER_ID AS Own_ID
, x.LAST_OWNER_ID     , x.CREATED_BY Created_By_Name   , x.FINAL_CHECK_BY                , x.LAST_HUMAN_MODIFIED_ID, x.ORIGINAL_LEAD_ID
, x.RELATED_CONTACT_ID, x.CONSULTANT_ID
```

### 1. List Mode (`"layoutMode": "list"`)

**Output**
```sql
  x.FIRST_REP_ID
, x.FIRST_REP_NAME
, x.SECOND_REP_ID  AS Sec_Sales_Rep
, x.SECOND_REP_NAME
, x.SOLUTION_REP_NAME
, x.BUSINESS_REP_ID
, x.ACCOUNT_MGR_ID AS Account_Mgr
, x.SPECIALIST_1
, x.SPECIALIST_2
, x.OWNER_ID       AS Own_ID
, x.LAST_OWNER_ID
, x.CREATED_BY     AS Created_By_Name
, x.FINAL_CHECK_BY
, x.LAST_HUMAN_MODIFIED_ID
, x.ORIGINAL_LEAD_ID
, x.RELATED_CONTACT_ID
, x.CONSULTANT_ID
```
