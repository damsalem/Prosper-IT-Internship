# Prosper-IT-Internship
## Introduction
Immediately after completing the Tech Academy's full stack developer bootcamp, I attended and completed an internship at Prosper I/T Consulting. It was a great opportunity to work with a development team of my peers on a full-scale MVC project.

This project focused on building an **Employee Scheduling Web App**. When completed, it would allow new employees to set their schedules, notify their bosses and team of time off, as well as allow their bosses to track clock in/out times.

I saw first-hand how code can evolve over time as it goes through the Software Development Life Cycle (SDLC). For example, I noticed stories coded and completed only to later be adjusted as they were used. **One of my favorite parts was working together with my teammates collaborating and solving stories as interesting problems arose.**

Over the two week sprint I also had the opportunity to work on project management and team programming skills that I'm confident I will use again and again on future projects.

Much of the project was brand new when I got to it, so many of the stories were simple and procedural. However, I've included some of the more interesting code I was able to work on. Below are descriptions of the stories I worked on, along with code snippets and navigation links. 

## Added a Clickable Mail Icon Using Glyphicons
The navbar on the page needed a link to the mail section. Rather than using text, we decided on including a bit of flair and chose to use an icon. Creating a navbar link was easy, so the first hurdle came with deciding on an image. After testing out PNGs, I thought a built-in option might work well and with some quick research, discovered Bootstrap 3 included glyphicons. The next hurdle was adjusting the formatting to fit a glyphicon and after some testing, I found the solution below.
```
<body>
    <div class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <img class="navbar-brand" src="~/Content/Images/The Tech Academy Logo-converted.png" alt="Tech-Academy Logo"/>
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                @Html.ActionLink("Schedule-It", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
                    <li class="nav-item dropdown">
                        <a class="nav-link dropdown-toggle" href="#" id="navbarDropdown" role="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
                            Navigation
                        </a>
                        <div class="dropdown-menu" aria-labelledby="navbarDropdown">
                            <a class="dropdown-item"> @Html.ActionLink("Schedule", "Index", "Schedule")</a>
                        </div>
                    </li>
                    <li><a href="@Url.Action("Mail", "Index", "Home")"><i class="glyphicon glyphicon-envelope"></i> Mail</a></li>
                </ul>
                @Html.Partial("_LoginPartial")
            </div>
        </div>
```
*Jump to*: [Top of Page](https://github.com/damsalem/Prosper-IT-Internship/blob/master/README.md#prosper-it-internship), [Other Skills](https://github.com/damsalem/Prosper-IT-Internship/blob/master/README.md#other-skills-i-learned)
## Fixed the Time Off/Delete Bug
I discovered that anyone who tried to delete a Time Off Event ran into an odd bug. It seemed that the data being passed wasn't of the right type. After quite a bit of trial and error, I was able to adjust the arguments in multiple places (including those below) and it worked brilliantly.
```
// GET: TimeOffEvent/Delete/5
        public ActionResult Delete(Guid id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
            TimeOffEvent timeOffEvent = db.TimeOffEvents.Find(id);
            if (timeOffEvent == null)
            {
                return HttpNotFound();
            }
            return View(timeOffEvent);
        }

        // POST: TimeOffEvent/Delete/5
        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public ActionResult DeleteConfirmed(Guid id)
        {
            TimeOffEvent timeOffEvent = db.TimeOffEvents.Find(id);
            db.TimeOffEvents.Remove(timeOffEvent);
            db.SaveChanges();
            return RedirectToAction("Index");
        }

        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                db.Dispose();
            }
            base.Dispose(disposing);
        }
```
## Changed a Function so the Start Property Was Set to the Current Time
When an employee creates a time-off event in the calendar, he had to select the time in each and every case. This made for an unfriendly UX and so I changed the Start Property to a DateTime.Now.
```
// POST: WorkTimeEvents/Create
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create([Bind(Include = "EventID,Start,End,Note,Title,ActiveSchedule,ApproverId,Id,ClockFunctionStatus")] WorkTimeEvent workTimeEvent)
        {
            if (ModelState.IsValid)
            {
                //Changed the Start parameter so it is set to current time.
                workTimeEvent.Start = DateTime.Now;
                workTimeEvent.EventID = Guid.NewGuid();
                db.WorkTimeEvents.Add(workTimeEvent);
                db.SaveChanges();
                return RedirectToAction("Index");
            }

            return View(workTimeEvent);
        }
```
*Jump to*: [Top of Page](https://github.com/damsalem/Prosper-IT-Internship/blob/master/README.md#prosper-it-internship), [Other Skills](https://github.com/damsalem/Prosper-IT-Internship/blob/master/README.md#other-skills-i-learned)
## Create a ScheduleFromTemplate Method
When a new employee was brought on, they needed to set their schedule in this system. Rather than creating a new schedule each and every time, templates could be used to setup a basic framework. This method would allow such templates to be used.
```
/// Create a schedule from a template 
        /// *NOTE* The template table is currently empty
        public Schedule ScheduleFromTemplate(string TemplateId, string UserId)
        {
            Schedule schedule = new Schedule(UserId);
            var scheduleTemplate = db.ScheduleTemplate.Find(TemplateId);
            schedule.ScheduledWorkPeriods = scheduleTemplate.ScheduledWorkPeriods;

            return schedule;
        }
```
*Jump to*: [Top of Page](https://github.com/damsalem/Prosper-IT-Internship/blob/master/README.md#prosper-it-internship), [Other Skills](https://github.com/damsalem/Prosper-IT-Internship/blob/master/README.md#other-skills-i-learned)
## Changed Multiple Views on a Controller so They Are Accessible to Logged in Roles Only
As with many programs, there are access levels and we wanted the schedules to only be visible to those who were logged in. I added the following code to prevent access to visitors who hadn't logged in with a Login Error redirect.
```
// GET: TempSchedule
        public ActionResult Index()
        {
            //Requires user is logged in to continue otherwise redirects you to shared LoginError page
            if (User.IsInRole("User") || User.IsInRole("Admin"))
            {
                return View(db.TempSchedules.ToList());
            }
            else return View("~/Views/Shared/LoginError.cshtml");
        }
// GET: TempSchedule/Create
        public ActionResult Create()
        {
            //Requires user is logged in to continue, otherwise redirects you to shared LoginError page
            if (User.IsInRole("User") || User.IsInRole("Admin"))
            {
                return View();
            }
            else return View("~/Views/Shared/LoginError.cshtml");
        }
```
*Jump to*: [Top of Page](https://github.com/damsalem/Prosper-IT-Internship/blob/master/README.md#prosper-it-internship), [Other Skills](https://github.com/damsalem/Prosper-IT-Internship/blob/master/README.md#other-skills-i-learned)
## Added Time Format Notes Alongside an Input Form
When an employee wanted to take time off, he needed to enter in the start and end date. I added in some simple HTML to improve the user-friendliness of this form.
```
                <div class="form-group">
                    @Html.LabelFor(model => model.Start, htmlAttributes: new { @class = "control-label col-md-2" })
                    <div class="col-md-10">
                        @Html.EditorFor(model => model.Start, new { htmlAttributes = new { @class = "form-control", @Value = DateTime.Now.Date} })
                        @Html.ValidationMessageFor(model => model.Start, "", new { @class = "text-danger" })
                        <p>MM/DD/YYYY</p>
                    </div>
                </div>
                <div class="form-group">
                    @Html.LabelFor(model => model.End, htmlAttributes: new { @class = "control-label col-md-2" })
                    <div class="col-md-10">
                        @Html.EditorFor(model => model.End, new { htmlAttributes = new { @class = "form-control", @Value = DateTime.Now.Date } })
                        @Html.ValidationMessageFor(model => model.End, "", new { @class = "text-danger" })
                        <p>MM/DD/YYYY</p>
                    </div>
                </div>   
```
*Jump to*: [Top of Page](https://github.com/damsalem/Prosper-IT-Internship/blob/master/README.md#prosper-it-internship), [Other Skills](https://github.com/damsalem/Prosper-IT-Internship/blob/master/README.md#other-skills-i-learned)
## Resolved Primary Key Bug
While testing the time off event as described above, I noticed that we were running into an error on the primary keys. After additional observation, I came to the conclusion that the unique keys were not automatically being created. The following code resolved that.
```
// POST: TimeOffEvent/Create
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create([Bind(Include = "Id,EventID,Start,End,Note,Title,ActiveSchedule,ApproverId,Submitted")] TimeOffEvent timeOffEvent)
        {
            if (ModelState.IsValid)
            {
                // Setting the submitted time to now
                timeOffEvent.Submitted = DateTime.Now;
                // Setting the eventID to a new unique GUID, without this, it is all 0s
                timeOffEvent.EventID = Guid.NewGuid();
                db.TimeOffEvents.Add(timeOffEvent);
                timeOffEvent.Id = HttpContext.User.Identity.GetUserId();
                db.SaveChanges();
                return RedirectToAction("Index");
            }

            return View(timeOffEvent);
        }
```
*Jump to*: [Top of Page](https://github.com/damsalem/Prosper-IT-Internship/blob/master/README.md#prosper-it-internship)
# Other Skills I Learned
* Working with a group of developers to identify front-end and back-end bugs to the improve usability of an application.
* Improving project flow and efficiency by communicating and separating areas of work according to features.
* Learning new efficiencies from other developers by observing their workflow and asking questions.
* Practicing pair programming—when one developer runs into a bug they cannot solve.
    * One of the developers was having troubles with the navbar formatting and subsequent bugs. Using Slack, I walked him through the code and he was able to sort it out and debug it in minutes.
    * I was struggling through debugging a Bootstrap Datepicker. A teammate took half of code and helped me sort through it and get it fixed.
*Jump to*: [Top of Page](https://github.com/damsalem/Prosper-IT-Internship/blob/master/README.md#prosper-it-internship)
