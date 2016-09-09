## Naming recommendation for feature branches

The name should be made of four optional strings separated by dashes:

1.  A common identifier used as prefix (e.g., gsoc2014) if applicable.
2.  The name of a significant package involved (an existing or a new
    one) if applicable.
3.  The name of the single component added or modified (e.g., class,
    function, concept, or enum) if applicable, or a description of the
    changes to see. Maybe prefixed with "fix" if it's a bugfix.
4.  The login username of the main developer or the name of a group
    (e.g., geometrica, vegas, mpi, eth, gf or forth) if applicable.

Examples:

-   Assume Jenny would like to develop a fast implementation of the 2D
    convex hull, the recommended name would be:

`Convex_hull_2-make_it_faster-jenny`

-   Sandeep is a GSoC student in 2014. He works on the plane-sweep code
    in the Arrangement\_on\_surface\_2 package. The recommended name is:

`gsoc2014-Arrangement_on_surface_2-plane_sweep-sandeep`

-   Adam is fixing a bug in the Mesh\_3 package. The recommended name is

`Mesh_3-fix_point_at_infinity-adam`

Notice that you will need to enter these names in places where you may
not have automatic name-completion. For example, if you are involved in
the development of several new features developed in different branches,
you may need to switch between the different branches often. Switching
between branches is a common routine for you that you would like to
facilitate. For GIT there exists tab completion of branch names and many
programs with GUIs.
