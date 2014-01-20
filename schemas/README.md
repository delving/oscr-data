
# Notes about Schemas

The OSCR schemas are structured as prototypes rather than something like
XSD, which is to say that the schemas look exactly like the documents
which are stored.  This makes them very easy to read by non-experts.

There are some things that appear frequently, such as vocabularies and
instances, which have implicit sub-tags, not explicitly visible in the
schemas.

## Implicit Fields : Vocabulary

Vocabularies consist of pairs of values: Identifier and Label.

So for example when something like this appears:

    <Language>{ "vocabulary": "Language" }</Language>

The implication is that the stored record will contain the following XML:

    <AcquisitionType>
       <Identifier>OSCR-PURCHASE</Identifier>
       <Label>Aankoop</Label>
    </AcquisitionType>

These values should match the values to be found in the XML files
storing the vocabularies themselves, where they are recorded as entries:

    <Entries>
        <Entry>
            <Identifier>OSCR-PURCHASE</Identifier>
            <Label>Aankoop</Label>
        </Entry>
        ....
    </Entries>


### Authority URIs

We can best create vocabularies of Dutch terms right away and later
determine how they are linked to terms in a terminology authority list.
The URI values pointing to the terminology can be added immediately by
hand to the XML files holding the vocabularies, or people can use
a user interface for setting these values.

Keep in mind that these things only really become relevant at the point
that you want to do advanced research querying on the data, so we need
not rush to make these connections.  In fact, the decisions should
be made in a careful and considered way by experts.

## Implicit Fields: Instance

Instances are intended to eventually become links pointing to records
either of shared data or of primary data.  The links need not be
established right away, but if they are available it would of course
be desirable.

During migration it may be the case that the source data is not normalized,
in which case it is better to leave the link blank and record any bits
of information which could be useful for later finding the link.

An instance field in the schema looks like this:

     <Person>{ "instance": "Person" }</Person>

In the records stored for this schema can either be furnished with
a link in the form of a URL:

     <Person>
        <Link>http://oscr.brabantcloud.eu/instance/shared/Person/[ID]</Link>
     </Person>

If, however, it is not yet clear which entity is being referred to, the
other approach can be taken which involves recording LinkFact entries,
which are intended to be human readable values which should aid in
the eventual searching for the actual Link.

     <Person>
        <LinkFact>
            <Name>GivenName</Name>
            <Value>Piet</Value>
        </LinkFact>
        <LinkFact>
            <Name>Surname</Name>
            <Value>Potlood</Value>
        </LinkFact>
        <LinkFact>
            <Name>DateOfBirth</Name>
            <Value>1963-03-25</Value>
        </LinkFact>
     </Person>

The Name portion of the link fact should be a limited and carefully
chosen value from a relatively small list, but it is open-ended in
the sense that the Name can really describe any fact that could
be helpful.



