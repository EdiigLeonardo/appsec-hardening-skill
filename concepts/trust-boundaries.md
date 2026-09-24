# Trust boundaries and isolation

At every boundary ask:

Who controls the input?; What identity crosses the boundary?; What assumptions are made?; Is authorization repeated?; Is the message authentic?; Can the sender impersonate another sender?; Can the boundary be bypassed?.

Review isolation between:

users; tenants; services; environments; networks; privileged and non-privileged execution.


## Environment isolation

Review:

development; test; staging; production.

Check:

credential separation; data separation; network separation; privilege separation; production data copied into lower environments; production access from developer machines.
