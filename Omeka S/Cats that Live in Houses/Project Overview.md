# Project Overview
## [Cats That Live in Houses](https://adhc.lib.ua.edu/fork/s/Cats/page/cats-in-houses)
Throughout history, cats have shared houses with folks. Today in the United States, cats live in houses in every neighborhood in every community all across the country. They live in rural houses and urban houses; poor houses and rich houses; with single person and multi-person households. These cats live full lives of mystery and shenanigans. This is a collection of the documentary record of the lives and experiences of cats that live in houses.

This project is an archive of the cats who live in our houses. The project was built as a sandbox at the Alabama Digital Humanities Center as a place for practice and training in the platform Omeka S. The project team includes librarians and library student employees who work closely with the ADHC as well as volunteer friends of the ADHC. 
## Site Layout
Content on the Omeka S site is organized into three resource types: item set (Cat Record), item (Cat Image Record, Person Record), and media (Media Record).

### Cat Record
Each cat is represented by a Cat Record that is categorized in Omeka S as an item set. This groups all information and media for a specific cat in one place. The following metadata is associated with each Cat Record through the **Cats Item Set** resource template:

- Name (dcterms:title): The cat's formal name
- Nickname (foaf:nick): Any nicknames associated with the cat
- Description (dcterms:description): A general description of the cat's appearance, history, and behaviors
- Birthday (foaf:birthday): The date the cat's birthday is celebrated
- Interests (foaf:interest): Any interests the cat has
- Relations (dcterms:relation): The people and/or cats this cat is associated with

Every Cat Record (item set) also contains the Cat Image Records (items) associated with that particular cat.

### Cat Image Record
Every image of a cat is represented by a Cat Image Record that is categorized in Omeka S as an item. This item is placed within the item set corresponding to the cat depicted in the image. The following metadata is associated with each Cat Image Record through the **Cats Item** resource template:

- Name (dcterms:title): A name for the associated image
- Description (dcterms:description): A description of what is happening in the image
- Creator (dcterms:creator): The person who captured this image
- Date (dcterms:date): The date this image was captured

Every Cat Image Record (item) contains the Image Record (media) that contains the actual image file and its associated information.

### Media Record
Every image file of either a cat or a person is represented by a Media Record that is categorized in Omeka S as media. This media is placed within the item corresponding to the specific image it contains. The following metadata is associated with each Media Record:

- File Name: The exact file name for the image (e.g., cat1.png, cat2.jpg)
- File Format (dcterms:format): The file extension for the image file (e.g., PNG, JPG)

### Cat Image Record
Every person is represented by a Person Record that is categorized in Omeka S as an item. This item is not placed within any item set and is instead only associated with other resources through the Relations property. The following metadata is associated with each Person Record through the **Cats Person** resource template:

- Name (dcterms:title): The name of the person
- Description (dcterms:description): A general description of the person
- Relations (dcterms:relation): The cats and/or people this person is associated with
- Image File Name The exact file name for the image (e.g., person1.png, person2.jpg)
- Image File Format (dcterms:format): The file extension for the image file (e.g., PNG, JPG)

## Contributing to the Project
Contributors can add cats to the project through a Google Form that accepts up to 3 cats, with 3 associated images each, in a single submission.

### Step-by-Step Procedure
1. Navigate to the submission form through the following link: https://forms.gle/VbnXqQSi2vzqJWi27
2. Enter information about yourself to create a Person Record that will be associated with any cats in the same submission
3. Enter information about a cat to create a Cat Record.
4. Attach image files for the cat and provide associated information to create Cat Image Records and Media Records
5. Use branching for multiple entries to upload more images of the current cat or begin a new section for a separate cat.
6. Submit the form. Your response will be loaded onto a spreadsheet for review and eventually be processed onto the site.
