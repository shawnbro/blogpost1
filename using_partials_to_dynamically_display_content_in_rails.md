# Using Partials to Dynamically Display Content in Rails
### <em>If you're looking for a quick and easy solution to dynamically render data in your Rails views, consider using partials.</em>
<br>
<p>Dynamically displaying data can be confusing, especially when our model and controller logic is written in Ruby, while we attempt to use Javascript/Ajax to make GET requests to update our views.  </p>
<br>
<br>

<p> The following example is a "Conference Room Schedule App".  In the example, a user picks a date and time slot and the calendar displays the booked appointments appropriately. A user should be able to scroll between weeks and the view should update, without reloading the page. </p>

<p> This is how the final product looks: 
<br>
![Calendar View]"/images/calendar-view"

</p>
  