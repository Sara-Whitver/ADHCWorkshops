# Modular CSV Import Macro
This macro reorganizes responses from a Google Form into a format that can be imported into an Omeka S site with the CSV Import module. It is designed to support projects with different ontological structures and forms with multiple branching sections.

## Overview
The responses to a Google Form can be imported as an automatically updating worksheet in a Google Sheets spreadsheet, where each submission is stored as a single row. When a form allows contributors to submit multiple related records, such as multiple cats and pictures, the resulting row contains separate columns for every possible entry.

This macro reorganizes this data into individual records and generates an output sheet using the headers and section mappings specified in the configuration.

The macro uses three sheets:

- **Form Responses 1:** Contains the original Google Form responses.

- **Split Responses:** Separates the submitted information into individual records for inspection.

- **CSV Import:** Contains the final output, ready to be exported and imported into the destination platform.

## Setting up the Spreadsheet

To process form responses, the form must be linked to a Google Sheets spreadsheet where the script can access the data.

1. **Link the Form to Sheets:** Open your Google Form in edit mode, navigate to the Responses tab, and click **Link to Sheets**

2. **Choose Workbook Destination:** Select **Create a new spreadsheet** or select an existing Google Sheet workbook to append the response tab into.

3. **Verify Sheet Creation:** Open the newly linked Google Sheet and confirm that the responses appear in a sheet, typically named `Form Responses 1`.

## Creating the Macro and Setting a Trigger

To continually format new responses, the macro must be added with a trigger set to execute it whenever the spreadsheet is opened.

1. **Open Apps Script:** Inside your Google Sheet, navigate to **Extensions** $\rightarrow$ **Apps Script**.

2. **Paste the Script:** Replace any default code in the `Code.gs` file with the modular script. Remember to click the **Save** icon.

3. **Create an Automatic Trigger:**
-  In the left sidebar of the Apps Script editor, click **Triggers** (the alarm clock icon).

- Click **+ Add Trigger** in the bottom-right corner.

    * Under **Choose which function to run**, select your main execution function, `createMacroOutput`
    * Under **Select event source**, choose **From spreadsheet**.
    * Under **Select event type**, choose **On open**.
    * Click **Save**

## Macro Configuration
All project-specific settings are located in the CONFIG object at the beginning of the script.

### Sheet Names
The `sheets` section contains the names of the three worksheets used by the macro.
- `source` is the sheet containing the original form responses.

- `split` is the sheet used to inspect the separated records.

- `output` is the sheet containing the final import data.

These names must match the corresponding worksheet tabs in Google Sheets. The source sheet must already exist, while the other two sheets are created automatically if necessary.

### Output Headers
The `outputHeaders` list defines the columns in the final CSV Import sheet. Each entry corresponds to a column in the output, such as Resource Type, Parent Item Set, or a metadata property. Any metadata properties must match their corresponding IDs (e.g. dcterms:title, foaf:interest).

When changing the headers, update the record mappings in the relevant sections to use the new header names. The header names in these mappings must match the configured output headers exactly.

### Root Section
The `rootSection` setting identifies the main record in each form submission. For example, each submission to Cats that Live in Houses contains information about a person and their cats, so person is its root section. 

All other sections would be connected to it through the children setting. Note that this is only to provide organization within the code and is not representative of the ontological structure within Omeka S, which can be specified later.

The root section also specifies `startColumn`, which determines the first column containing its information.
Column numbers start at 1, meaning column A is 1, column B is 2, and so on.

### Form Sections
Each entry in the `sections` object represents a type of information collected by the form. For example, Cats that Live in Houses contains three form sections.

- **Person:** A record containing information about the person submitting the form. This is the root section for the project.

- **Cat:** A record containing information about a specific cat associated with the person submitting the form.

- **Image:** A record containing information about a specific image of the cat it is associated with.

### Section Settings
Each section has several settings that determine where its information is located in the original response.

- `label` provides a readable name for the section.

- `startColumn` specifies the absolute starting column for the root section.

- `fieldWidth` specifies the number of columns used for the section's own fields.

- `blockSize` specifies the distance between the starting positions of repeated copies of this section, if it repeats. It must be at least as large as `fieldWidth`.

- `repeat` specifies the maximum number of time this section can repeat in one parent block.

- `gapBefore` specifies how many unused columns appear between the end of the parent section and the beginning of the following child section.

- `gapBetween` specifies how many additional unused columns appear between repeated copies of the section.

- `requiredField` identifies a field that must contain a value for the record to be processed.

- `fields` defines the fields collected by the section.

- `record` defines how the section maps to an output record.

- `media` defines how an associated file is represented in the output, if applicable.

- `relations` specifies how relationships are generated.

- `children` lists the sections that branch from this section.

Settings that are not needed by a particular section can be omitted where the processing code supports their absence. However, the code expects `fieldWidth`, `blockSize`, `repeat`, and `fields` for each section.

### Column Positions and Spacing
The macro uses column offsets rather than requiring every field to have an absolute column number. This allows the form layout to be changed by adjusting a few settings instead of modifying the processing code.

#### Starting Column
The root section uses `startColumn` to specify its absolute position in the response sheet. For example, if the root sections starts in column B, the setting should be set to `startColumn = 1`.

#### Field Width
The `fieldWidth` setting specifies the reserved width of a section's own fields. For example, if a person section uses eight columns, its configuration would contain:
`fieldWidth: 8`. The first child section begins after those eight columns, plus any gap set by `gapBefore`.

#### Gap Before a Child Section
The `gapBefore` setting specifies how many unused columns appear between a parent section's field area and the first column of a child section.

For example, if a parent section occupies columns B through I:

- `gapBefore: 0` starts the child section in column J.

- `gapBefore: 3` skips over columns J through L and starts the child section in column M.

#### Block Size
The `blockSize` setting controls the starting position of the next copy of a repeated section. It is measured from the starting position of one section to the starting position of the next copy.

For example, a section may span 30 columns between its own information and all possible child sections underneath it. Therefore, a setting of `blockSize = 30` would tell the next copy of the section to begin 30 columns later, plus any gap set by `gapBetween`.

#### Gap Between Repeated Sections
The `gapBetween` setting adds extra unused columns between repeated copies of a section.

For example, with the following settings: `blockSize = 30`, `gapBetween = 2`. Each repeat of a section begins 32 columns after the previous copy. The 2 columns specified by `gapBetween` are skipped when reading the form submission.

### Field Mapping
The `fields` object identifies the fields collected by each section and their positions within that section. For example:
```
fields: {
  name: { offset: 1 },
  description: { offset: 2 },
  date: { offset: 3 }
}
```
The code above identifies three fields where `name` is in the first column of the section, `description` is in the second column, and `date` is in the third column. Offsets start at 1 and are relative to the section's starting position.

The field names are internal identifiers used by the code. They do not need to match the form's question labels or the output metadata headers.

When changing a form's column layout, update the offsets to match the new locations of the fields.

### Child Sections
The `children` setting determines which sections are associated with each parent. For example:

```
person: {
  children: ["cat"]
}

cat: {
  children: ["image"]
}
```

This creates a hierarchy of person, cat, and image records. A section can have multiple children, and a child can contain its own children. The processing code follows this hierarchy recursively so all sections must be either a root section, or a child somewhere in the heirarchy under the root section. Circular relationships between sections will not function correctly.

### Output Records
The `record` object maps the fields collected by a section to the columns in the final output.
For example:

```
record: {
    "Resource Type": { value: "item" },
    "dcterms:title": { field: "title" },
    "Parent Item Set": { parentField: "name" },
}
```

This mapping supports three main types of values:
- `value`: Inserts a fixed value
- `field`: Inserts a field from the current section
- `parentField`: Inserts a field from the parent section

The mapping can also use { relation: true } to insert the relationship value generated for the current record.

`Resource Types and Templates`
The resource type and resource template are determined by the record mapping. For an Omeka S project, resource types commonly include item, item set, and media.

Resource templates can be changed to match the templates configured in the destination site.

### Attached Media
A section can include a media object to generate an additional output row for a media resource. For example:

```
media: {
  fileName: "fileName",
  fileFormat: "fileFormat",
  title: "title",
  parentItem: "title"
}
```

These settings specify which fields contain the file name and format, which field provides the media title, and which field identifies its parent item. The macro generates a media row only when the file name field contains a value.

### Generated Relations
The relations setting determines how the macro generates relationships between records. The current implementation supports three rules. For example:

```
relations: {
rule: "field",
field: "relations"
},

relations: {
rule: "parentRelations"
},

relations: {
rule: "parentRelationsPlusParent"
}
```

- `field` copies a relations value directly from a field in the current section.

- `parentRelations` inherits the relations value from the parent section.

- `parentRelationsPlusParent` inherits the parent's relationships, removes the current record's name from the list, and ensures the parent record's title is included.

These rules are intended to cover the relationship structure used by the example archive. Projects with different relationship requirements may need additional rules.

### Formatting
The formatting settings control how text values are handled before they are written to the output. For example:

```
formatting: {
replaceSemicolons: true,
trimText: false
},
```

- `replaceSemicolons` replaces semicolons with commas in string values.

- `trimText` removes whitespace from the beginning and end of string values.

These settings apply to the split data and the generated output. They do not modify the original form responses.

## Output Sheets
### Split Responses
The Split Responses sheet provides a readable representation of the date from the original form submissions.

Each row represents one record and contains:

- Submission Row: The row number of the original response.
- Section: The type of record.
- Parent Section: The type of record associated with it.
- Parent Title: The title of its parent record.
- Section Fields: The configured fields for each section

Fields are given names such as person_name or cat_description so that fields with the same internal name can be distinguished.

### Code Workflow
When the macro runs, it performs the following steps:

1. Reads the form responses from the source sheet.

2. Checks the configuration for common errors.

3. Generates the Split Responses sheet.

4. Processes each response according to the configured section hierarchy.

5. Creates the main output records and any associated media records.

6. Maps the records to the configured output headers.

7. Writes the final data to the CSV Import sheet.

The CSV Import sheet can then be exported as a CSV file for use with the destination import tool.

### Adapting the Macro
When adapting the macro to a new project, review the following settings in order:
1. Update the source, split, and output sheet names.

2. Set the root section and its starting column.

3. Define each section and its field offsets.

4. Set the field widths, block sizes, repeat counts, and spacing.

5. Connect the sections using the children lists.

6. Update the output headers and record mappings.

7. Configure media records if the project uses uploaded files.

8. Select or extend the relationship rules if necessary.

10. Test the output with several responses, including responses with missing optional sections and multiple repeated records.

The macro is intended to reduce the amount of code that must be changed when a project uses a different form layout. Most changes should be made in the configuration rather than in the processing functions.

However, the configuration must still reflect the actual form layout and the requirements of the destination import tool. New relationship behaviors or fundamentally different output structures may require changes to the processing code.