# WordNet 3.0, version for SQL Server 2005

This is the [Princeton Wordnet](http://wordnet.princeton.edu/) (version 3.0) which I have converted into a relational database in Microsoft SQL Server (version 2005). I also took the opportunity to clean up some of the data (duplicate entries, circular references). The database is in an MDF file, which you can simply attach to your SQL Server.

## Data sources

- **WordNet 3.0, provided by Princeton University**. This database contains data from the Prolog version of the Princeton WordNet 3.0 (downloaded 12 March 2010). The WordNet data in this database are a complete and unedited copy of the WordNet dataset with the exception of several obvious errors, such as duplicate entries -- such errors have been manually corrected in this database and are noted below in this readme file.

Plus the following "standoff" files, also provided by Princeton University:
- **The Morphosemantic Database** (semantic relations between morphologically related nouns and verbs), downloaded 22 March 2010
- **The Teleological Database** (an encoding of the typical activity or purpose for which an artifact was intended), downloaded 22 March 2010

## Tables in the database

tblSynsets
- derived from wn_g.pl
- each row represents a synset.
- There were 3,621 duplicate entries in the file from which this table was derived. These have been removed.

tblSenses
- derived from wn_s.pl
- each row represents a word sense.
- The entry for 'aerial {adjective}' at line 184584 in the Prolog file had no sense number and no tag count. This has been corrected such that the sense number is 1 (the only other entry for 'aerial {adjective}' has sense number 2) and the tag count is 0.
- The entry for 'marine {adjective}' at line 184586 in the Prolog file had no sense number and no tag count. This has been corrected such that the sense number is 4 (the other entries for 'aerial {adjective}' have sense numbers 1, 2, 3 and 5) and the tag count is 0.
- There were 5,580 duplicate entries in the file from which this table was derived. These have been removed.

tblSenseKeys
- derived from wn_sk.pl
- each row represents the fact that the specified word sense can be uniquely identified by the specified string ("sense key").

tblHypernymy
- derived from wn_hyp.pl
- each row represents the fact that one synset (the hyponym) is a a hyponym of another synset (the hypernym).
- There are known to be some circular chains of references in this table.

tblMeronymy
- derived from wn_mm.pl, wn_mp.pl, wn_ms.pl
- each row represents the fact that one synset (the meronym) is a meronym of another synset (the holonym).

tblInstance
- derived from wn_ins.pl
- each row represents the fact that one synset (the instance) is an instance of another synset (the type).

tblClasses
- derived from wn_cls.pl
- each row represents the fact that one synset or one word in that synset (the member) is a member of a class represented by another synset or by one word in that synset (the class).
- There were 110 duplicate entries in the file from which this table was derived. These have been removed.

tblAdjSyntax
- derived from wn_syntax.pl
- each row represents the fact that the specified adjective can only be used in the specified syntax:
	p	predicate position
	a	prenominal (attributive) position
	ip	immediately postnominal position

tblFrameList
- derived from a list given in the Prolog package documentation (wninput.5WN)
- gives a list of all possible verb frames ("somebody ----s something" etc.)

tblVerbFrames
- derived from wn_fr.pl
- represents the fact that a given verb in a given synset (or all verbs in that synset, if word number is 0) are used in the specified frame.

tblEntailment
- derived from wn_ent.pl
- represents the fact that a verb synset (the entailer) entails another verb synset (the entailed).

tblCausation
- derived from wn_cs.pl
- each row records the fact that a verb synset (the causer) cases another verb synset (the caused).

tblVerbGroups
- derived from wn_vgp.pl
- each row records the fact that a verb sense (or the whole verb synset) (the "from") is similar to another verb sense (or another whole verb synset) (the "to").
- Each pair of senses/synsets, A and B, is recorded in the table twice: once as "from A to B" and once as "from B to A".

tblAdjClusters
- derived from wn_sim.pl
- each row represents the fact that an adjective synset (the satellite) is a member of a cluster headed by another adjective synset (the head). Synsets in a cluster are similar in meaning.

tblAttributes
- derived from wn_at.pl
- each row represents the fact that a noun synset (the attribute) has as its value an adjective synset (the value).
- In the file from which this table was derived, each attribute-value pair was listed twice, once as "from attribute to value" and once as "from vaue to attribute". This duplicity has been eliminated in the SQL version: the table only contains the "from attribute to value" pairs.

tblAntonymy
- derived from wn_ant.pl
- each row represents the fact that two word senses are antonymous.
- WordNet documentation says that each pair of senses, A and B, is recorded in the table twice: once as "from A to B" and once as "from B to A". That certainly is the case for most of the sense pairs that appear in this table. There are, however, 5 pairs for which this not true. Because I'm not sure what WordNet's intention was, I have not attempted to fix it in any way.

tblParticiples
- derived from wn_ppl.pl
- represents the fact that an adjective word sense is a participle of a verb word sense.

tblPertainymy
- derived from a subset of wn_per.pl
- each row represents the fact that an adjective word sense (the "pertainer") pertains to another adjective or noun sense (the "pertained-to").

tblAdvDerivations
- derived from a subset of wn_per.pl
- each row represents the fact that an adverb word sense is derived from an adjective word sense.

tblSeeAlso
- derived from wn_sa.pl
- each row represents the fact that additional information about the first word sense (or the whole synset if word number is zero) (the "source") can be obtained by looking at the second word sense (or synset, it word number is zero).

tblDerivations
- derived from wn_der.pl
- each row represents the fact that there exists a derivational relationship between the word senses.
- It would seem that each pair of word senses, A and B, is supposed to be listed twice in the Prolog file (and in this table), once as "from A to B" and once as "from B to A". That certainly is the case for most of the sense pairs that appear in this table. There are, however, 113 pairs for which this not true. Because I'm not sure WordNet's intention was, I have not attempted to fix it in any way.

tblMorphosemantic
- derived from the Morphosemantic Database
- each row represents the fact that a verb sense (the "source") is morphosemanticly related to a noun sense (the target).
- the semantic nature of the relation is named in the Relation column
- all sense pairs included in this table are also included in tblDerivations

tblTeleology
- derived from the Teleological Database
- each row represents the fact that there is a teleological link between two word senses
- the semantic nature of the link is named in the Relation column
