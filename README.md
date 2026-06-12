The line that weighed heavily on me from the documentation was 'Your API design and architecture should be focused on meeting the functional
requirements outlined above.' along with the requirement to be maintainable and simple. As a result, I decided to focus in on a simple way to complete the Post action in the controller with a bank client. Maybe client isn't a great name.


Model validation
I opted for data validations. My first instinct was to build a custom validator completely. I was reasoning that it it would then be easy to extend and use a factory of some flavour if banking was non-standard for cards, across countries as an example. 
So when a request can in some sifting might happen to then validate against the correct validator. However, I felt this was in danger of being over engineered for the task at hand
and that it could also be possible to provide endpoints per variation so I opted for the simpler built in Data Annotations.
This did give me an interesting challenge to work out how to validate across 2 fields for dates but that worked out ok I think and I appreciated the diversion.
While I understand fluid validation is well liked I felt for the task at hand and how I interpreted the rejected response requirement it seemed a little faffy in this use case.
I think I have a slight preference to use Microsoft packages if they work due to the chance another package could go out of maintenance at some point.
A note on types. I moved card number and cvv to string types on the incoming. I was careful to read the doc and it wasn’t explicit o the types. CVVs can start with 0 so this made them feel safer to me as strings. Using a regex to ensure they are strings with numeric values isn’t too difficult to get the effect but it stops leading 0s from being dropped. Card numbers needed changing from int to long anyway due to size and possible values and I felt it might be possible to have a future requirement to accept card numbers with space or – separators and this would be not far off that.  

Limitations of the choice I made using Data Annotations is that I need to configure API behaviour of when doing the validation which feels a little awkward. I also in rejecting based on card number don’t return the accurate last 4 entered necessarily. This could be an issue for the merchant working out why it was rejected, especially when coupled with not saving rejected data but the supplied repo doesn’t save a whole card number anyway so this felt acceptable given the scope and requirements.

IDs
I took a steer here from the docs saying a GUID is fine. I have an assumption that in fact the Checkout systems will be more distributed and that an ID would be better in prod using something with timestamp, node id, client id and what have you but that would be 
excessive for this test maybe. As it's fairly simple the DI and interface feel a touch overblown but I wanted to make it easy to switch out to show what I felt should be

Repository
Again, this was taking guidance form the specification. As a result, I didn't fiddle too much to deal with the slightly odd not actually async get request in payments controller. Simply added something to deal with id not found.
I also haven't been saving rejected requests which fail validation. I assumed they should be saved for audit reasons as well as debugging and helping client merchants improve their process. However here I felt it was better
to skip it and keep the repository simple and untouched.

Dependency Injection and config
It was important to inject the bank client and id generator. As there was only one of each, I didn’t go the full config route of using appsettings.json to name the classes to use as I feel this isn’t awful to do when a second client is added that you may need to switch between. I did however do something with the base URL for the bank to demonstrate as it’s very standard to have URLs by environment.

I think a concern I have with this architecture namely the BankModule folder is a little unsatisfying. It should have a better name and be its own project but due to my rustiness with C# I felt that was a little ambitious for the time constraints and the effect it would have currently. Also IRL it could be it’s own API depending on if there was a micro services choice made. The naming was hard because it’s just generic bank in the supplied material.


