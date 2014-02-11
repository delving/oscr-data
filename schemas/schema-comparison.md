# Schema Comparison

This document looks at the differences between the schemas which we are now using and the ones
which were created by deriving more directly from the FORTH models.  The former schemas work in
OSCR while the latter were intended to be somewhat close to usable in OSCR but in need of
adjustment.  Our intention is to find out if there are any essential things missing in the
current schemas. Whenever things resulted from discussions with Annette Gaalman and Jolanda van der
Akker, they will be identified by "EB" here.

It is important to note that we can potentially still make deep changes to the schemas
that we use, since the migration has only been partially completed, and it is a process which
can be repeated during the current test phase.  In reality, FORTH has also discovered issues
with their schemas from time to time, and has had to make changes in data as a result of
fixing them.

It is also important to realize that it is nearly effortless to expand existing schemas
within a running system, and much more difficult to contract them.  Expanding involves
no changes to the data whatsoever.  This is a hint to start small and expand later.

## General Comments about differences

The derived schemas are intended to be fully exhaustive, describing every aspect of the stored
record, whereas there are a number of implicit structures in the actual working OSCR schemas.

### Instance Fields

When instances are pointed to, the derived schemas have special attributes named to record
the "id" and the "type", with the expectation that an explicit link will be available from
the beginning, and until then the field will be empty or just contain one string.

In the OSCR schemas there is just a field which contains "instance" in its configuration, which
implies that there can be certain things recorded within.  There will soon be a description
available of how the instance system in OSCR works, but briefly:

This is the place where the LinkFact entries can be placed if
there is no link available (during migration), and when an actual link is made, these fields
contain the "header" of the pointed-to document.  The header contains the instance's identifier,
schema, so an actual URI or URL can be fabricated from those, once we know exactly how these
items will be referred to.  The header also has SummaryFields which are no more than cached fields
which are also present in the Body, but they can make display quicker because immediate lookup
is not needed.  There is also a timestamp which can be compared to the Shared record to
see if anything cached has changed in the meantime.

### Multiple kinds of instances

In the derived schemas we assumed that multiple kinds of instances could be recorded in a
given field. Configuration is then:

    { "lookup": [ "person", "place", "event", "photo" ] }

The current OSCR schemas separate these out into multiple fields, each with their own
type of instance.

    <!--to whom does this photo refer? -->
    <Person>{ "instance": "Person" }</Person>

    <!--to which organization does it refer?-->
    <Organization>{ "instance": "Organization" }</Organization>

    <!--to what location does it refer?-->
    <Location>{ "instance": "Location" }</Location>

    <!--does it refer to a particular historical event?-->
    <HistoricalEvent>{ "instance": "HistoricalEvent" }</HistoricalEvent>

The reason for this was simply to sidestep some complexity for the time being.  It is now
possible to make it so that instances can be chosen only after the instance type has first
been chosen.  In other words, these can now be integrated into one field, once we have done
the necessary software changes.

    <Entity>{ "instance": ["Person", "Organization", "Location", "HistoricalEvent"] }</Entity>

It could look like the above, after the changes are made.

## Person

The derived Person schema has RelatedToPerson, we now have Mother, Father, and Child (multiple).
Also, the minimal Period block with BurthDate/DeathDate has been expanded with things
related to the funeral etc.  Marriage events were added where they were missing.  SignificantLifeEvent
was added as an extra that can be left blank if there is nothing notable.

## Notes on Specific Fields (Photo as reference)

The following sections are comments about specific fields in the schemas and why
they have been added/changed/removed from the schemas we use now.

### IdentificationNumber

Documents in OSCR are stored in two parts: header and body.  The body of the record does not
contain this "fixed" identification number because it is the Identifier part of the header.

### Authenticity, Condition, Purpose

These were deemed unnecessary by EB, and removed to avoid confusing people from the HKKs.

### CreationEvent -> Creation

This block contains a "Type" which EB did not understand, and there was no field to record
the Role of a given creator, rather just a number of creators without role.  Also, the
ObjectRelated block was not understood, and Technique/Material were considered to not
relevant for Photo, for example.  Instead, the PhysicalObject block was given a
Format and Material, and a free list of other PhysicalProperty entries so that any
extra information could be recorded if it was available.

As suggested by FORTH, a block within DigitalObject containing a free list
of "TechnicalProperty" entries was added, to avoid the elaborate TechnicalDescription
with fixed fields.

### StorageLocation / Type

There was no idea at EB what this type would mean, and what they really needed was a text
field to describe the actual location beyond the "map" location, such as shelf.  The
StorageLocation was put inside the PhysicalObject block because it is about the actual
physical thing, not the Photo in general.

### ReferenceTo -> RefersTo

This block in the derived records contained a Subject field, but EB felt that this was
better called ReferenceType.  The OtherRelatedEvents seemed strange as well since there
was there was already a ReferenceTo which contained "event".  In the derived schema
the ReferenceTo was singular - the RefersTo was made multiple, which seems more appropriate
because it could refer to a number of things.

### Source

The derived schemas had Source with IdentificationNumber and AnyOtherNumber, which was
confusing to EB.  They didn't know what this would mean and they wanted it removed.  Instead,
an Acquisition block was added, which was not found in the derived schemas.

### OtherIdentificationNumbers

This block with its attribution of who assigned the identification number was considered
by EB to be overkill in the HKK context.  We could add a "multiple" section for
IdentificationNumbers if that makes sense.

### Exhibition

This was missing in the derived schemas and EB considered it to be an important thing
to record.  It records an event when the thing was exhibited.

### IsReferencedBy

The EB people did not understand how this would be filled in, and in the derived schemas
this part was somewhat incomplete, involving some kind of suggested lookup in a
bibliography.  We didn't know what to do with it so it was removed for clarity.

## BookObject -> Publication

There was a need to capture more than just books, and since most of the info about
a book can be similar to what is needed for publications in general, we decided to
bring publications together in one schema.  Irrelevant things can of course be left blank.

### OtherIdentificationNumbers

Similar to the above section with the same title, we could do this if necessary.

### IncludedIn/IsComposedOf -> Contains/IsContainedIn

Grammatically these names are better, since the "Is" was not matched in "IncludedIn".  Otherwise
these are essentially the same, only the new schema entries refer to other Publication instances.

### Subtitle

I had this included, but EB insisted that it be removed.  They may have changed their minds
since.

### ManagementCategory

Nobody had any idea what this meant so it was removed.

### Collection

This was just a text field, and EB didn't know what it would mean.  Presumably a collection
could also be considered a Publication and then Contains could be used.

### CompositionType

It was unclear what this meant.

### StorageLocation

What they really wanted for this, again, is just a "Place" on the map, as well as a field to
describe freely where to find it at that place.

### Publication

The Publication block of the derived schemas was hard to understand and explain.  There
is a "type" of publication, the Publisher was considered to be just a Person (not organization?)
and the Place strangely contains GeopoliticalHierarchy (cached indication of the place) and
a link whereas we now could just link to the Location instance.  I see that there is no
Location in there now, so we could add that if there is data for it.

### Acquisition

The acquisition should have more than the derived schema's PersonInvolved, so we now have
organization as well.  FORTH also suggested that there be a Condition field to describe
the condition upon acquisition.

### Copyright

In the derived schemas there was no notion of copyright.  Was it missing?  We added
a Copyright block and took the perhaps doubtful assumption that it could be described
by CreativeCommons.  Maybe that should be removed.

### OriginatorOfReference/InHouse

WTF, not sure what these are about, omitted it.