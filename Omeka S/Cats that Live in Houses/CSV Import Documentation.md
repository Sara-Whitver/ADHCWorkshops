# CSV Import Documentation
The Cats That Live in Houses project is an archive of cats that live in people's houses. It was created by the Alabama Digital Humanities Center to serve as a platform for training in the use of Omeka S.

## Overview
Contributors submit information and pictures of their cats to a Google Form. This responses to this form are stored and updated in a Google Sheets workbook. An Apps Script macro attached to this workbook then reorganizes this data into a format that can be imported into an OmekaS site through the CSV Import module.

## Google Form
The Google Form for the site is the primary source of data for the site. It gathers information about a person, their associated cats, and related media. The form collects three tiers of information:

### Person Info:
- Name
- Description
- Relations (e.g., a list of associated cats)
- Uploaded image file and its technical metadata (file name and format)

### Cat Info:
- Name
- Nickname
- Description
- Birthday
- Interests

### Cat Image Info:
- Name
- Description
- Creator
- Date
- Uploaded image file and its technical metadata (file name and format)

The form allows for the submission of multiple cats with multiple associated images within a single response. It does so by utilizing conditional branching (e.g., asking "Do you have any more images of this cat to upload?" or "Do you have any more cats to upload?").

However, because of this structure, a single response exports as one row of data with a separate column for every possible data entry. This format serves well for allowing a single form to handle all submissions, but must ultimately be reformatted to allow it to be uploaded to Omeka S.

## Form Responses

The **Form Responses 1** sheet serves as the source of raw data for the workbook. Whenever a contributor completes the form, a row containing the corresponding data is appended to this sheet. This results in a table with columns representing every question on the form and rows representing every response received. A timestamp is also recorded for when each submission occurs.

## Splitting Responses

The macro's first processing step extracts the single-row responses from **Form Responses 1** and separates them into multiple entries per response.

Just one form submission can contain information on multiple cats, with multiple images each, all contained in a single row of data. However, the CSV Import module requires each person, cat, and image entry to be in a separate row. Therefore, the macro must extract these sections from the raw data and store them as individual rows.

Rather than just storing this split table in its memory, the macro writes the data to the **splitResponses** sheet. This step provides the following benefits:
- Clear Data Visibility: Converting the wide structure of the raw data into separate entries creates a more readable layout where all the cat and image information associated with each contributor can be easily inspected.
- Error Spotting: If a user submits an unexpected input, the greater readability of the **splitResponses** sheet can allow administrators to easily catch errors before generating the final CSV Import file.

## 3. Record Types
Once the responses have been split up, the macro assigns each separate entry with a specific record type, depending on what information is present. This assigned record type exists to guide the vocabulary mapping process and generate accurate resource type/template values.

- **Person Records** contain information about the person
- **Cat Records** contain information about each cat
- **Cat Image Records** contain descriptive information about an image
- **Media Records** contain information about the actual image file

The most significant change here is that for each Cat Image Record and Person Record, there is also an associated Media Record.

## Assigning Resource Type/Template
Based on record type, the macro assigns two Omeka S properties, resource type and resource template.

Omeka S organizes all objects into three main resource types: item, item set, and media. Depending on the record type, each entry is assigned to one of these categories:
- **Person Record → Item** 
- **Cat Record → Item Set** 
- **Cat Image Record → Item** 
- **Media Record → Media**

Each resource can also be assigned a resource template to standardize what metadata properties it expects to be assigned. Depending on record type, each entry is assigned its corresponding resource template.
- **Person Record → Cats Person** 
- **Cat Record → Cats Item Set** 
- **Cat Image Record → Cats Item**

Media Records are not assigned a resource template as they contain very few metadata properties.

## Relations
As the macro reads the separated responses, it also keeps track of how the different records are related. Using the relationships defined for the person, it automatically generates the relationships for the associated cats. It also assigns parent item sets for items and parent items for media.

### Extrapolating Relations
The form only asks the contributor to submit relations between a person and their associated cat(s). The macro extrapolates this to also generate relations for the individual cats, both back to the person but also to the other cats in the same response.

### Parent Relations
The macro also generates parent relations for items and media to allow them to be contained within their associated item sets and items respectively.
- Each **Cat Image Record** is assigned a value containing the name of the **Cat Record** it is under
- Each **Media Record** is assigned a value containing the name of **Cat Image Record** or **Person Record** it is under.

## Vocabulary Mapping
The macro then maps the form's user-friendly headers and newly generated properties to the metadata vocabularies required by Omeka S, primarily Dublin Core Terms (dcterms:) and Friend of a Friend (foaf:). These terms are listed below:
### General Properties:
- Resource Type (Specifies what type of resource the entry is: item set, item, media)
- Resource Template (Specifies what resource template to assign to the entry)
- Parent Item Set (Specifies what item set **Cat Records** belong to)
- Parent Item (Specifies what item **Media Records** belong to)
### Person Record:
- Name → dcterms:title
- Description → dcterms:description
- Relations → dcterms:relation
- Image File Name (Used to identify media source files through File Sideload)
- Image File Format → dcterms:format

### Cat Record:
- Name → dcterms:title
- Nickname → foaf:nick
- Description → dcterms:description
- Birthday → foaf:birthday
- Interests → foaf:interest
- Relations → dcterms:relation

### Cat Image Record:
- Name → dcterms:title
- Description → dcterms:description
- Creator → dcterms:creator
- Date → dcterms:date

### Media Record:
- File Name (Used to identify media source files through File Sideload)
- File Format → dcterms:format

## Creating the Final Output:
Once the macro has finished processing the data, it populates the appropriate headers and writes the final table to the **CSV Import** sheet. This serves as the sheet that will be exported as a .csv file to be imported into Omeka S through the CSV Import module.