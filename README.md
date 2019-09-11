# Daedalus
- Team
	- Niklas - @NiklasEi
	- Tobias - @cryptobias
	- Albert - @alikhuber
	- Gauthier - @6ry0u
-  Tech Stack chosen: [Evan.network](https://evan.network/)
- Context/Industry: aviation supply chain, especially after sales business
- Customers/stakeholders
	- Airlines
	- Maintenance and Repair Organization (MRO)
	- Spare parts suppliers/manufacturers
	- Aviation Authorities
- Problems/pain points to solve
	- Long life cycles (50 years before aircraft retirement): how to track  and trust the operations history and all the ownership transfers over such a long time? How to audit it?
	- High safety risk related to wrong or late maintenance operations
		- E.g. low cost airline keeping operating an aircraft past the maximum legal replacement date of a part
	- High safety risk related to bogus parts
	- High safety risk related to aircraft operation out of specified/legal conditions
		- E.g. airline/airport allowing take off beyond maximum allowed take off weight (to transport more cargo and save money although illegal)
	- Manual and paper based process
- Benefits
	- Safer aircraft operations
	- Streamlined and better scheduled maintenance process: perform maintenance operations neither too early nor too late but at the right time
		- costs savings + process efficiency
		- avoid fines
		- prevent reputation and brand damage
	- Automation possibilities if combined with IoT devices: digital twin ordering its "self-replacement" on behalf of the Airline
- Solution: front-end client connecting to evan.network and allowing customers to perform their specific jobs
	- Airline
		- Check parts installed and their properties (e.g next planned maintenance operation date)
		- Transfer ownership of aircraft to another Airline
		- Check history of flights
		- Request/schedule maintenance operation
	- MRO
		- Perform maintenance task
		- Order spare part
	- Supplier
		- Deliver spare part
	- Authority
		- View history of maintenance operations
		- View hisotry of aircraft flights

## Data model
Aircraft = AC  
Maintenance & Repair Organization = MRO  
## Stored (ipfs + references "on chain")
|Digital Twin|Property|Type|C|R|U|D|
|--|--|--|--|--|--|--|
|AC|msn|int|AC manufacturer|public||
|AC|parts|list(text)|owner|AC owner|AC owner, authorizedMros||
|AC|cat|text|AC manufacturer|public|||
||||||||
|part|id|int|
|part|msn|int||AC owner
|part|certificate||AC owner, allowedMros|AC owner, allowedMros
|part|allowedMros|list(str)|AC owner|AC owner|AC owner|
|part|lastMaintenanceDate|int|allowedMros|AC owner, allowedMros|AC owner, allowedMros
|part|maxAgeAllowed|int|AC manufacturer|AC owner, allowedMros|AC owner, allowedMros

## Computed at runtime in the Dapp front end
|Digital Twin|Property|Type|C|R|U|D|
|--|--|--|--|--|--|--|
|part|maintenanceDatePast|bool|||internal
|AC|takeOffAllowed|bool|||internal||

## Requirements/Features
- AC can be transferred from manufacturer to airlines and between Airlines
- AC owner can update parts list
- `takeOffAllowed` gets updated automatically after comparing `maxAge` against the difference of `lastMaintenanceDate` and `today`
- Only allowed MROs or the owner of the AC the part is installed on can update the `lastMaintenanceDate`
- Only allowed MROs or the owner of the AC the part is installed on can update the the corresponding `parts` list of the AC object
