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

## Notes on Specific Fields

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