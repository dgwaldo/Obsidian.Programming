
- Add a default mapping to the enums
	- Throw and DomainException("Unmapped {enumType} enum value")

- Drop Auto Mapper in favor of doing parsing data from DTO to the Domain Model ensuring values are doing the right thing.

- Drop Fluent Validation if validation can be done in the mapping.

- Fix our Unit of Work pattern to handle transactional concerns properly.

* User Fakes instead of MOQ
	* Create mocks of repos with collections to replace mocks for db 
	
* Pull SQL Creation / Migration out into its own repository as this gives more flexibilty allowing SQL admins to help out without needing all the other code stuff.

* Tight up the value objects.

