Scala Slick type-safe ids
=========================

Slick (the Scala Language-Integrated Connection Kit) is a framework for type-safe, composable data access in Scala. This library adds tools to create views that can be easly used. Users needs only to declare how view and filter should like and Beholder provides methods for filtering data.


Contributors
------------
Authors:
* [Krzysztof Romanowski](https://github.com/romanowski)

Feel free to use it, test it and to contribute!

Getting beholder
---------------

For latest version (Scala 2.10.x compatible) use:

```scala
libraryDependencies += "org.virtuslab" %% "beholder" % "0.1.2"
```

Or see [Maven repository](http://maven-repository.com/artifact/org.virtuslab/beholder_2.10).

Examples
========

Lets assume that we created simple entries

```scala
case class MachineId(id: Long) extends AnyVal with BaseId
```
```scala
case class UserId(id: Long) extends AnyVal with BaseId
```

and junction table joined them by ids.


Defining view
-----------------

Usually for data in view does not came form single table so we have to create view that is materialisation of some query. Beholder provieds such utility. We can create Slick's table that operate on view (it is read only).

```scala
case class UserMachineView(email: String,
                             system: String,
                             cores: Int)

val usersMachinesQuery = for {
      user <- Users
      userMachine <- UserMachines if user.id === userMachine.userId
      machine <- Machines if machine.id === userMachine.machineId
    } yield (user, machine)


val UsersMachineView = FilterableViews.createView(name = "USERS_MACHINE_VIEW",
      apply = UserMachineView.apply _,
      unapply = UserMachineView.unapply _,
      baseQuery = usersMachinesQuery) {
      case (user, machine) =>
        //naming the fields
        ("email" -> user.email,
          "system" -> machine.system,
          "cores" -> machine.cores)
    }
```
