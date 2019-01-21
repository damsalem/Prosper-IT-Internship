# Prosper-IT-Internship
## Introduction
Immediately after completing the Tech Academy's full stack developer bootcamp, I attended and completed an internship at Prosper I/T Consulting. It was a great opportunity to work with a development team of my peers on a full-scale MVC project.

I saw first-hand how code can evolve over time as it goes through the Software Development Life Cycle (SDLC). For example, I noticed stories coded and completed only to later be adjusted as they were used. **One of my favorite parts was working together with my teammates collaborating and solving stories as interesting problems arose.**

Over the two week sprint I also had the opportunity to work on project management and team programming skills that I'm confident I will use again and again on future projects.

Much of the project was brand new when I got to it, so many of the stories were simple and procedural. However, I've included some of the more interesting code I was able to work on. Below are descriptions of the stories I worked on, along with code snippets and navigation links. 

## Added a Clickable Mail Icon Using Glyphicons
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
                    <li><a href="@Url.Action("Mail", "Index", "Home")"><randoName class="glyphicon glyphicon-envelope"></randoName> Mail</a></li>
                </ul>
                @Html.Partial("_LoginPartial")
            </div>
        </div>
```
*Jump to*: [Top of Page](https://github.com/damsalem/Prosper-IT-Internship/blob/master/README.md#prosper-it-internship)
## Changed a Function so the Start Property Was Set to the Current Time
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
*Jump to*: [Top of Page](https://github.com/damsalem/Prosper-IT-Internship/blob/master/README.md#prosper-it-internship)
## Create a ScheduleFromTemplate Method
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
*Jump to*: [Top of Page](https://github.com/damsalem/Prosper-IT-Internship/blob/master/README.md#prosper-it-internship)
## Changed Multiple Views on a Controller so They Are Accessible to Logged in Roles Only
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
```
*Jump to*: [Top of Page](https://github.com/damsalem/Prosper-IT-Internship/blob/master/README.md#prosper-it-internship)
# Other Skills I Learned
* Working with a group of developers to identify front-end and back-end bugs to the improve usability of an application.
* Improving project flow and efficiency by communicating and separating areas of work according to features.
* Learning new efficiencies from other developers by observing their workflow and asking questions.
* Practicing pair programming—when one developer runs into a bug they cannot solve.
    * One of the developers was having troubles with the navbar formatting and subsequent bugs. Using Slack, I walked him through the code and he was able to sort it out and debug it in minutes.
    * I was struggling through debugging a Bootstrap Datepicker. A teammate took half of code and helped me sort through it and get it fixed.
