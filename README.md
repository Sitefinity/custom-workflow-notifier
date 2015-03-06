Custom workflow notifications in Sitefinity
==========================
Sitefinity's workflow pipeline sends email notifications when content is waiting for approval and when content was rejected. In most cases the default notifications do everything you want. However, sometimes you may want to change the text of the email message, add/remove subscriber, or even notify via another channel like SMS or dashboard.

So, how do you inject your own code into workflow notifications?
With Sitefinity version 8.0, you can do it by descending from **WorkflowNotifier** and register you new class with the **ObjectFactory**.

## WorkflowNotifier
This class has 2 overridable methods:
* **void SendNotification(WorkflowNotificationContext)** base class implementation generates email subject and body and sends the notification to the recipients in the context. You can trigger your own notification here, and/or call the base implementation.
* **void BeforeEmailSend(WorkflowNotificationContext context, ref string subjectTemplate, ref string bodyTemplate, ref IDictionary&lt;string, string&gt; tokens, ref IEnumerable&lt;ISubscriberRequest&gt; subscribers)** is called when emails are about to be sent. You can update email's subject and body templates and also add/remove/change a replacement token. Also you can change the list of subscribers, or cancel email sending if you empty this list.

## CustomWorkflowNotifier example.
The example in this repository is a fully functional class derived from the standard **WorkflowNotifier**. The example demonstrates how you can add your code, which sends custom email message and adds another recipient to the list.

## Prerequisites
* Sitefinity 8.0 or above

## Build and install
Add to your Global.asax:

```C#
    public class Global : System.Web.HttpApplication
    {
        protected void Application_Start(object sender, EventArgs e)
        {
            Bootstrapper.Initialized += this.Bootstrapper_Initialized;
        }

        void Bootstrapper_Initialized(object sender, Telerik.Sitefinity.Data.ExecutedEventArgs e)
        {
            if (e.CommandName == "Bootstrapped")
            {
                ObjectFactory.Container.RegisterType<Telerik.Sitefinity.Workflow.Activities.IWorkflowNotifier, Telerik.Sitefinity.Samples.CustomWorkflowNotifier.CustomWorkflowNotifier>();
            }
        }
    }
```

## API Overview
### WorkflowNotificationContext
The **WorkflowNotificationContext** class has several properties, which hold information about the workflow event that triggered the notification. The following table provides more details about each property.

<table>
	<thead>
		<tr>
			<td><strong>Type</strong></td>
			<td><strong>Property</strong></td>
			<td><strong>Description</strong></td>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td><strong>IEnumerable&lt;ISubscriberRequest&gt;</strong></td>
			<td><strong>Subscribers</strong></td>
			<td>
				<p>
					A collection of the subscribers of the notification.
				</p>
			</td>
		</tr>
		<tr>
			<td><strong>string</strong></td>
			<td><strong>ApprovalStatus</strong></td>
			<td>
				The current status (awaiting, rejected, approved...) of the content item. See **ApprovalStatusConstants** for possible values.
			</td>
		</tr>
		<tr>
			<td><strong>string</strong></td>
			<td><strong>ApprovalNote</strong></td>
			<td>
				The note written by the last status changer. For example if the content was rejected, then it will hold the rejection reason.
			</td>
		</tr>
		<tr>
			<td><strong>string</strong></td>
			<td><strong>ItemTypeLabel</strong></td>
			<td>
				A localized (in default BackEnd culture) name of item's type.
			</td>
		</tr>
		<tr>
			<td><strong>string</strong></td>
			<td><strong>ItemPreviewUrl</strong></td>
			<td>
				A URL where the item is shown in preview mode.
			</td>
		</tr>
		<tr>
			<td><strong>string</strong></td>
			<td><strong>ItemTitle</strong></td>
			<td>
				 The name of the content item.
			</td>
		</tr>
		<tr>
			<td><strong>string</strong></td>
			<td><strong>SiteName</strong></td>
			<td>
				The name of the site where item will be published.
			</td>
		</tr>
		<tr>
			<td><strong>string</strong></td>
			<td><strong>SiteUrl</strong></td>
			<td>
				The URL of the site where item will be published.
			</td>
		</tr>
		<tr>
			<td><strong>DateTime</strong></td>
			<td><strong>ModificationTime</strong></td>
			<td>
				The time when the item was last modified.
			</td>
		</tr>
		<tr>
			<td><strong>Guid</strong></td>
			<td><strong>LastModifierId</strong></td>
			<td>
				The id of the user who last modified the content item. Can be Guid.Empty if user is unknown.
			</td>
		</tr>
		<tr>
			<td><strong>Guid</strong></td>
			<td><strong>LastStateChangerId</strong></td>
			<td>
				The Id of the user who last changed the approval status of the content item.
			</td>
		</tr>
		<tr>
			<td><strong>string</strong></td>
			<td><strong>LoginUrl</strong></td>
			<td>
				The URL where one can log into site's BackEnd.
			</td>
		</tr>
		<tr>
			<td><strong>WorkflowDataContext</strong></td>
			<td><strong>ActivityDataContext</strong></td>
			<td>
				The DataContext of the currently executing workflow activity.
			</td>
		</tr>
	</tbody>
</table>

