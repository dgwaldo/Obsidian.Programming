Validation or enforcement of invariants in DDD falls into two camps. Exception throwing v.s. returning some form of error messages. Of the two methods, I prefer returning error messages.  One thing to note is that using something like an .IsValid() method, by definition of its mechanics, makes it impossible to keep the object from being put into an invalid state. 

## Tracking Validation Errors




