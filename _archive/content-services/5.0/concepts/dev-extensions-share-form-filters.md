---
author: Alfresco Documentation
---

# Form Filters

Form filters can be used to modify the properties that are available to the form control implementation. They are often used to set up properties that should control some behaviour in a custom form control implementation.

|Extension Point|Form Filters|
|---------------|------------|
|Architecture Information|[Share Architecture](dev-extensions-share-architecture-extension-points.md).|
|Description|Form filters are typically used to add custom processing to a `FormProcessor`. Here are a number of use-cases:

-   Adding fields before the form is generated.
-   Adding calculated fields after the default set of fields have been processed.
-   Generating a unique identifier for a field before it's stored in the database.
-   Generate XML rendition of the metadata after the form data is stored in the database.
-   Check field values before they are stored in the database.

A filter registry is associated with each `FilteredFormProcessor` implementation, each registered `Filter` is then called \(within the same transaction\) for each request processed by the `FormProcessor`.

 It is the responsibility of the `Filter` to determine whether it is applicable for the request. The order the Filters are executed is not guaranteed.

 Implementing a Form Filter requires a bit of Java programming. The following is an example of a Form filter that adds a property called `prop_dueDateReadOnly`, which is then used in a custom [Form Control](dev-extensions-share-form-controls.md) to determine if a Due Date should be read-only or not:

```
public class TaskFormFilter extends AbstractFilter<WorkflowTask, WorkflowTask> {
    public static final String DUEDATE_READONLY_PROP_NAME = "prop_dueDateReadOnly";

    @Override
    public void beforeGenerate(WorkflowTask workflowTask, List<String> fields, List<String> forcedFields,
                               Form form, Map<String, Object> context) {
        if (workflowTask != null) {
            boolean dueDateReadOnly = false;

            final FieldDefinition dueDateReadOnlyFieldDef = new PropertyFieldDefinition("dueDateReadOnly", "d:boolean");
            dueDateReadOnlyFieldDef.setDataKeyName(DUEDATE_READONLY_PROP_NAME);
            dueDateReadOnlyFieldDef.setProtectedField(true);
            dueDateReadOnlyFieldDef.setLabel("Due Date Read-only");
            form.addFieldDefinition(dueDateReadOnlyFieldDef);
            form.addData(DUEDATE_READONLY_PROP_NAME, dueDateReadOnly);
        }
    }

    @Override
    public void afterGenerate(WorkflowTask workflowTask, List<String> fields, List<String> forcedFields,
                              Form form, Map<String, Object> context) {}

    @Override
    public void beforePersist(WorkflowTask workflowTask, FormData fieldDatas) {}

    @Override
    public void afterPersist(WorkflowTask workflowTask, FormData fieldDatas, WorkflowTask workflowTask1) {}
}
```

By extending the `AbstractFilter` class the filter gets automatically registered with the forms processor. The form filter interface is parametrized so we can specialize the data type that the form is for upfront: `Filter<ItemType, PersistType>`. The above filter has been created assuming the form is for a Workflow Task: `AbstractFilter<WorkflowTask, WorkflowTask>`

 After the filter class has been implemented we need to define a Spring Bean for it and at the same time specify which form processor we want to use:

 ```
<bean id="org.alfresco.training.repo.form.filter.taskFormFilter"
      class="org.alfresco.training.repo.forms.processor.TaskFormFilter" parent="baseFormFilter">
    <property name="filterRegistry" ref="taskFilterRegistry" />
</bean>
```

 In this case we are using the `taskFilterRegistry` as we are dealing with a Workflow Task forms that are processed by the `TaskFormProcessor`. There are other filter registries that you need to use for form filters that deal with objects from content models:

-   `NodeFormProcessor`: use the `nodeFilterRegistry`
-   `TypeFormProcessor`: use the `typeFilterRegistry`

 Defining a filter for a content model item forms looks something like this:

 ```
public class ContentItemFormFilter extends AbstractFilter<Object, NodeRef> {
    @Override public void beforeGenerate(Object item, List<String> fields, List forcedFields,
                                         Form form, Map<String, Object> context) {}
    @Override public void afterGenerate(Object item, List<String> fields, List forcedFields,
                                        Form form, Map<String, Object> context) {}
    @Override public void beforePersist(Object item, FormData data) {}
    @Override public void afterPersist(Object item, FormData data, NodeRef persistedObject) {}
}
```

 This filter would be registered via the following Spring bean definition:

 ```
<bean id="org.alfresco.training.repo.form.filter.contentItemFormFilterEdit"
      class="org.alfresco.training.repo.forms.processor.ContentItemFormFilter" parent="baseFormFilter">
    <property name="filterRegistry" ref="nodeFilterRegistry" />
</bean>
```

 This filter will actually only be invoked when you edit the content item \(Node\), if you want to also have the filter to be invoked when creating a content item \(Node\), then you have to also register it as follows:

 ```
<bean id="org.alfresco.training.repo.form.filter.contentItemFormFilterCreate"
      class="org.alfresco.training.repo.forms.processor.ContentItemFormFilter" parent="baseFormFilter">
    <property name="filterRegistry" ref="typeFilterRegistry" />
</bean>
```

 **Note**: You can't use the `nodeFilterRegistry` or `typeFilterRegistry` for Document Library Actions - they are only there for node creation, view and edit.

 The different methods that can be overridden, such as `beforeGenerate`, gives you the possibility to hook into the forms processing. They have the following meaning:

-   `beforeGenerate`: Callback invoked before the form is generated/created. This is the place to add properties that should be used by for example form controls.
-   `afterGenerate`: Callback invoked after the form has been generated for the given items and fields.
-   `beforePersist`: Callback invoked before the form data is stored in the database. This method can be used to for example check the form fields.
-   `afterPersist`: Callback invoked after the form data is stored in the database.

|
|Deployment - App Server|Form filter implementations does not lend themselves very well to be manually installed in an application server.[Build a Repository AMP](../tasks/alfresco-sdk-tutorials-amp-archetype.md) instead.

|
|[Deployment - SDK Project](../tasks/alfresco-sdk-tutorials-amp-archetype.md)|-   repo-amp/src/main/java - put the Form Filter class somewhere under this directory \(**Note** that a form filter should be include in a Repo AMP as it is to be part of the Alfresco Repository \(alfresco.war\)\)
-   repo-amp/src/main/amp/config/alfresco/module/repo-amp/context/service-context.xml - put the Form Filter Spring bean here.


|
|More Information|-   [Forms](forms-intro.md)

|
|Tutorials|??|
|Alfresco Developer Blogs|??|

**Parent topic:**[Share Extension Points](../concepts/dev-extensions-share-extension-points-introduction.md)

