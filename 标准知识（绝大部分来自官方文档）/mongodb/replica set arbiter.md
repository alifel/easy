# replica set arbiter

In some circumstances (such as when you have a primary and a secondary, but cost constraints prohibit adding another secondary), you may choose to add an arbiter to your replica set. An arbiter participates in elections for primary but an arbiter does not have a copy of the data set and cannot become a primary.

