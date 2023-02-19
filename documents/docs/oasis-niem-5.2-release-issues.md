## NIEM 5.2 and OASIS Options

### Option 1: Do Nothing with NIEM 5.2

Steps:

- Move forward with NIEM 6 under OASIS
- Leave NIEM 5.2 and earlier classic releases where they are

Pros:

- No additional work needed, just move forward with NIEM 6

Cons:

- Nothing to show as an OASIS product until we start having NIEM 6.0 PSDs, ETA Spring 2023
- User-friendly to the NIEM community 
	-  Valid NIEM releases and specifications split across multiple sites
	- Users won't be able to easily diff changes across different repos

### Option 2: Promote 5.2 to an OASIS PS

Steps:

- Optionally copy older NIEM releases and specifications _as-is_
	- Needs to be first for all the commits to flow correctly
- Copy the NIEM 5.2 schemas and specs to an OASIS repo _as-is_
	- Copy the NIEM Classic releases to the niemopen/models repo
	- Add license (looks like it's already there)
	- Tag the updated 5.2 version as something, don't know what
	- Optionally update README file to provide context?
	- Formatting specifications for OASIS
		- Hard to gauge time involved
		- CTAS as easy
		- NDR may take longer
- Submit for PGB approval as PSD
	- 14 days notification, then 3 day call for objections
- Submit for PGB approval as PS
	- 14 days notification, then 3 day call for objections
- Move forward with NIEM 6

Pros:

- Small lift to copy repos
- Gets a classic NIEM release into OASIS quickly

Cons:

- NIEM Classic 5.2 specifications and NIEM Open 5.2 PS will have documentation differences and won't be the same
	- Specification formatting, particularly for the NDR, will take time
- NIEM 5.2 will continue to take up some time for potentially the next two months as we move it through the OASIS process
	- Based on notification and call for objection waiting periods
- Going to a PSD isn't worth much, so really need to go to PS
	- PS would require also including the specifications
		- _All_ the specifications?
		- That's a heavy lift
	- Means more work and time
	- Timeline of formatting + approval + approval could take 3 months
- The schemas _must_ remain _as-is_
	- Any change in the schemas will force generation of the whole NIEM suite, including
		- All schemas
		- All tools
		- Some, if not all, specifications
	- Particularly, all schemas use URIs beginning with `http://release.niem.gov/`
		- That could be seen as an issue for something under OASIS
		- That cannot be changed in the 5.2 schemas
		- URI namespaces will not resolve to OASIS locations
	- We do not have the resources to generate a second version of NIEM 5.2 without a huge impact on NIEM 6
	- We do not want two different versions of NIEM 5.2

### Option 3: Start the OASIS process with NIEM 6.0, with 5.2 as initial contribution

Steps:

- Optionally copy older NIEM releases and specifications _as-is_
	- Needs to be first for all the commits to flow correctly
-  In January, initialize the NIEM 6.0 version
- Copy NIEM 5.2 artifacts _as-is_ into OASIS repos
	- Start with the NIEM Classic 5.2 schemas
	- Update versions to 6.0
	- Update the readme for OASIS
	- Tag the commit for alpha 0 (niem-6.0alpha0)
	- Submit the 6.0 alpha 0 tag as OASIS release for PGB approval
		- 14 days notification, then 3 day call for objections
- Move forward with NIEM 6

Pros:

- This is what would happen anyway with NIEM 5.2 being the starting point for NIEM 6
- Gets a NIEM Open release available to the OASIS community as soon as possible
- Because the artifacts (schemas and specifications) are expected to change as NIEM 6 develops, the _as-is_ nature isn't a problem
	- Can OASIS-fy the NDR _after_ technical changes (like Schematron rules) are nailed down
	- Format later as resources permit
- NIEM 5.2 is the same between NIEM Classic and NIEM Open
- NIEM 5.2 is done, we can immediately begin work on 6.0 version
- Clean break between NIEM Classic and NIEM Open, starting with the 6.0 version of the model and specs

Cons:

- No OASIS version of NIEM 5.2


