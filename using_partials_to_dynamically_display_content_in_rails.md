# Using Partials to Dynamically Render Content in Rails
### <em>If you're looking for a quick and easy solution to dynamically render data in your Rails views, consider using partials.</em>
<br>
<p>Dynamically displaying data can be confusing, especially when our model and controller logic is written in Ruby, while we attempt to use Javascript/Ajax to make GET requests to update our views.  </p>
<br>

<p> The following example is a "Conference Room Schedule App".  In the example, a user picks a date and time slot and the calendar displays the booked appointments appropriately. A user is able to scroll between weeks and see the appointments scheduled for that week, without having to reload the page. This can be a simpler alternative to using a library like Backbone.js</p>

<p> This is how the final product looks: 
<br>
<img src='https://raw.githubusercontent.com/shawnbro/blogpost1/master/images/calendar-view.png' "Calendar View" style='width: 100%;')

</p>
<p> **On our model layer,** we have appointments & users.  The calendar partial, which renders our appointments for the week specified, and is saved as _calendar.rb, looks like this: 


	- @weekdays.each do |day|
	  .day{:class => "color-#{@weekdays.index(day) % 2}"}
	    .day-label{:id => day}
	      = day.strftime("%A")[0..2]
	      = day.to_s.split('-')[1..2].join('/').remove(/\A0/).gsub(/\D(0)/, '/')
	    - @appointments.each do |appointment|
	      - if appointment.date == day
	        - position = ((appointment.start_time.strftime("%k%M").to_i - 800)/100 * 76 +13)
	        .appointment{:style => "margin-top:#{position}px"} 
	          .time
	            = (appointment.start_time.strftime("%l:%M %p -") + appointment.end_time.strftime("%l:%M %p")).remove(/:+0{2}/)
	          .user
	            = @user.first_name
<p> Never mind all the fancy GSUBbing & math, what is important is that we want to be able to scroll between weeks and have the view update, without reloading the page.  In order to do this, we've attached event listeners to the two arrows which surround the date near the top of the image. Inside calendar.js we have:

	$( document ).ready(function() {
		var week = 0;
	  
	  $('div.js-next-week').click(function() {
	    week += 1;
	    getDates(week);
	  })
	
	  $('div.js-prev-week').click(function() {
	    week -= 1;
	    getDates(week)
	  })
	}) //end doc ready

& our 'getDates(week) function looks like this: 

	function getDates(week) {
	  var newDates = $.get( "/appointments/new_week", { week: week }, function(response) {
	    $('.dates').html(response)
	    var monday = new Date($('div.day-label')[0].id)
	    var friday = new Date($('div.day-label')[4].id)
	    $('.week-scroll-display').empty().text( months[monday.getMonth()] + " " + monday.getUTCDate() + ' - ' + friday.getUTCDate() )
	      .append("<span id='year'> "+friday.getUTCFullYear()+"</span>" );
	  })
	}
	
As you can see, we make a GET request to the '/appointments/new_week' route using AJAX, and pass along our new week as an argument.  In the controller, we can see what our 'new_week' method looks like:

	def new_week
	  @user = User.find_by_id(session[:user_id])
	  @weekdays = (Date.today + params[:week].to_i.week).all_week.to_a[0..4]
	  @appointments = Appointment.all
	  render partial: "calendar", locals: {appointments: @appointments} 
	end
Rendering the partial 'calendar' returns the HTML content (with the updated date) to our view layer, and we just simply replace the content of the calendar div with the response.

The advantage to this approach is that it provides a quick and efficient way of updating our views on the fly, without having to rewrite/duplicate too much code in Javascript.  We already have our calendar view written in Ruby, so let's just access and re-render it as simply as possible.

