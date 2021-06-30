# Azure functions

## Create your local project
1. Choose the Azure icon in the Activity bar, then in the ***Azure: Functions*** area, select the ***Create new project***... icon.
2. Choose a directory location for your project workspace and choose ***Select***.
- >  Note
     These steps were designed to be completed outside of a workspace. In this case, do not select a project folder that is part of a workspace. 
3. Provide the following information at the prompts.
   - ***Select a language for your function project:*** Choose `Typescript`.
   - ***Select a template for your project's first function:*** Choose `Http trigger`.
   - ***Provide a function name:*** Type `HttpExample`.
   - ***Authorization level:*** Choose `Anonymous`, which enables anyone to call your function endpoint. To learn about authorization level, see [Authorization keys](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-http-webhook-trigger#authorization-keys).
   - ***Select how you would like to open your project:*** Choose `Add to workspace`.
 4. Using this information, Visual Studio Code generates an Azure Functions project with an HTTP trigger. Your can view the local project files in the Explorer. To learn more about files that are created, see [Generated project files](https://docs.microsoft.com/en-us/azure/azure-functions/functions-develop-vs-code#generated-project-files). 