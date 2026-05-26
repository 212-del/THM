so this was our first look of the room ip when opened up into a browser/

![image](homepage.png)

After looking at homepage it is asking some qustion and room too and their question relates somehow.

The room is saying about a hiest happened in a mueseum named Louvre Museum.

It is telling about the that glass entrance to the mueseum similar like a glass pyramid and its entrance name is Louvre Pyramid.

It is taking about a breach happened in the Galerie d'Apollon room of the museum Louvre Museum.

A group of people wearing hi-vis vests sneak in and light goes off for seven minutes and the team disappeared towards rivers.

And in the page of room ip gave by the room it is asked too that 

Which museum entrance by name is on the river side lines up where the ladder truck was positioned?

Give the entrance name and the official closure date for that entrance as stated by the museum.

As i asked the chatGPT that what are nearby entrance that were in past or present today to enter in the Louvre Museum that are along side the river. if it was functional earlier and not functional now what is that and tell me its official date of closure.

as after submitting the room ip page the answer

Seine-side_entrance-22_OCT_2024

And then moving to the next quesiton i was given some more info that is 

For Empress Eugénie's Reliquary Brooch, report:
- the official inventory number
- the surname of the maker (as named by the museum)
- the acquisition mode + year (e.g., assigned/purchased + year)
- the item's current location status on its record.

Reliquary Brooch is a special brooch that is of Empress Eugénie

When i Googled for the Empress Eugénie Brooch it gave me 2 brooch names 

Empress Eugénie owned several significant brooches, most notably the Diamond Bow Brooch and the Reliquary Brooch, both of which were stolen from the Louvre in October 2025. 

Meaning This is the same incident i think that is desribed here about the theft in this room.

After researching about the Reliquary Brooch, I got info a lot these are 

MV_1024 → the official inventory number (“MV 1024”)
BAPST → the maker’s surname, from “Alfred Bapst”
AFFECTE → acquisition mode, from French “affecté au Louvre” (assigned to the Louvre)
1887 → acquisition/assignment year
NON_EXPOSE → current status on the record, meaning “not on display”

So as per the answer format

Answer format: <INV>-<MAKER>-<MODE>-<YEAR>-<STATUS>
Note: Use UPPERCASE, no accents, spaces as underscores.
Example: OA1234-NAME-AFFECTE-1887-NON_EXPOSE

The answer will be : MV_1024-BAPST-AFFECTE-1887-NON_EXPOSE

Now as we move further towards investigation on the page We were present with new piece of information that are


Two of the stolen pieces received INTERPOL reference IDs on the public poster / database. What are the INTERPOL reference IDs for the Sapphire Diadem of Queens Marie-Amélie and Hortense, and the Reliquary Brooch?

Answer format: <REF1>,<REF2>
Example: 2022/23.5,2021/15.1

As it is being asked the refrence ID of public poster/database released by Interpol for the Sapphire Diadem of
   Queens Marie-Amélie and Hortense, and the Reliquary Brooch

And i Googled for Interpol reference ID for the Sapphire Diadem of
   Queens Marie-Amélie and Hortense, and the Reliquary Brooch and i got a pdf file in which the reference ID of both Items as well as Refence Number of Other Items were also mentioned in that PDF.

Here is the attached PDF.

[![PDF Preview](preview.png)](report.pdf)

This contained the ref ID For the both items that ref ID too was released by Interpol

They are below as per the answer format 

2025/359.1,2025/359.5

After proceeding further we are given a new piece of information that is 

Give the title, inventory number, and dimensions of the central ceiling painting in the Galerie d'Apollon.

Answer format: <title>-<inventory-number>-dimensions
Example: XXXX_XXXX-INV_1234-9mx10m

The Answer is below with explanation

Apollon_vainqueur_du_serpent_Python-INV_3818-8mx7.5m

This is the large central ceiling painting in the Galerie d'Apollon at the Louvre Museum. The work was painted by Eugène Delacroix in the 19th century to complete the gallery’s ceiling decoration. The painting depicts Apollo defeating the serpent Python, a mythological symbol of triumph and divine order.


As we move further we are given new piece of information that is 

What bridge lies directly south of the river-side nearest the aforementioned entrance?

Answer format: BRIDGE_NAME

After all answers will be correct we will be given a flag.

This is the first method of getting the flag but there is a another method too to get the flag.

When we saw the source code of the webpage we revealed some endpoints that are 

- /api/questions
- /api/submit-answers
- /api/submit-reports
- /api/success-message
