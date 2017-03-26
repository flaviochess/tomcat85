================================================================================
  Licensed to the Apache Software Foundation (ASF) under one or more
  contributor license agreements.  See the NOTICE file distributed with
  this work for additional information regarding copyright ownership.
  The ASF licenses this file to You under the Apache License, Version 2.0
  (the "License"); you may not use this file except in compliance with
  the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
================================================================================


       Construindo e Debugando Apache Tomcat no NetBeans


Ao contrário de outras IDEs, o NetBeans é um aplicativo de java swing puro. 
Ele usa o Apache Ant para construir seus projetos e trabalha diretamente 
com os arquivos .class e jars criados pelos arquivos padrão do build.xml 
do Apache Ant. Essa força é também a sua fraqueza ao trabalhar com projetos 
complexos como o Tomcat que já possuem seus próprios arquivos build.xml, 
mas que não usam os modelos do NetBeans.

Qualquer projeto Ant complexo ainda pode ser gerenciado no NetBeans 
definindo-o como algo chamado de Projeto de Formulário Livre. No entanto, 
como o build.xml não está em conformidade com todas as convenções de 
nomenclatura e estruturação do NetBeans, é necessária uma certa quantidade 
de personalização manual para obter um nível útil de integração.


1. O NetBeans pode abrir os fontes do Tomcat como um Projeto de formulário 
   livre (Free-Form Project), que permitirá editar, criar e depurar o Tomcat 
   e seus testes unitários dentro da bancada de trabalho. Mesmo com o 
   NetBeans 7.1, a integração de um projeto tão complexo como o Tomcat 
   requer uma configuração significativa. A configuração envolve lidar com 
   vários aspectos peculiares da maneira como o NetBeans gerencia esse tipo 
   de projeto. Antes de tentar abrir o Tomcat como um projeto NetBeans, é 
   altamente recomendável construir e executar com êxito os testes usando 
   o Apache Ant a partir de um prompt de comando! (Consulte BUILDING.txt 
   no diretório raiz de origem do Tomcat).

2. Uma vez que o Tomcat foi criado, você pode instalar a configuração 
   de projeto padrão do NetBeans executando o seguinte comando:

          ant ide-netbeans

   Isso usa o Tomcat build.xml para criar um novo diretório chamado 
   nbproject (esse nome é reservado pelo NetBeans). O diretório nbproject 
   será então iniciado com um conjunto autoconsistente de definições padrão 
   suficiente para abrir o Tomcat como um Projeto de Formulário Livre no NetBeans.

   Nota: se você abrir as propriedades do projeto no menu de contexto do 
   NetBeans, mesmo sem fazer alterações, há um risco significativo de que o 
   NetBeans modifique um ou mais desses arquivos. Você pode restaurar os 
   arquivos padrão do Tomcat a qualquer momento executando este target:

          ant ide-netbeans-replace

   Somente os arquivos padrão serão sobrescritos, portanto qualquer 
   outro conteúdo, como seus próprios arquivos locais e o diretório 
   particular do NetBeans, não serão afetados.

3. O NetBeans precisa saber onde encontrar o diretório com os jars de 
   dependências do Tomcat. Se você tiver criado o Tomcat com êxito a 
   partir do terminal, provavelmente terá atribuído a propriedade 
   base.path no arquivo build.properties.

   Atenção: O suporte para Tomcat no NetBeans detectará e usará essa 
            propriedade. No entanto, se você tiver deixado o padrão, 
            você ainda deve definir este caminho no arquivo 
            nb-tomcat-project.properties!

   Nota: O suporte atual para Tomcat no NetBeans não inclui os componentes 
         no diretório de módulos (por exemplo, tomcat-lite).

4. Start NetBeans... once it has initialised and scanned your other open
   projects, just open an existing project and select the location of
   Tomcat. NetBeans will recognise it as a Free-Form project, then read and
   validate the nbproject/project.xml file. This file defines how to relate
   targets in build.xml to NetBeans project-related actions. It also tells
   NetBeans what classpaths to use for validation and code completion of
   the various source directories.

   Warning: do not be tempted to casually click the properties menu item
            for the Tomcat project. NetBeans might change the contents
            of these files. (The NetBeans New Project wizard also
            automatically creates a Free-Form project.xml which carries
            this same warning).

   Note: the Tomcat project should open successfully and, after the source
         packages have been scanned, they should not be flagged with any
         syntax errors (except in some of the jsp bug unit tests).

5. Verify your work by running the NetBeans project Clean action. It
   should complete successfully. Next, run the Build action (which calls
   the Tomcat deploy build target) and confirm that it successfully
   compiles the Tomcat source files and creates the jars.

6. Next, navigate down to one of the test files and select the compile
   action. This will compile only your chosen file, although the compiler
   will find there is nothing to do unless you have deliberately changed it.

   Note: if you have changed any of the Tomcat source files, they will be
   recompiled first. However, any changes to test files will not be compiled
   unless you select those file and explicitly compile them. If you have any
   doubts about dependencies between unit test classes, you can use the
   compileAllTests project action and any files that have been changed
   will be detected and compiled.

7. You can run an individual unit test class by selecting it and choosing
   the "run selected file" NetBeans action. As the test runs, NetBeans
   should open a unit test results pane that shows you the progress and
   final outcome of the test (Note: this feature does not currently work).

8. Next, open the source of the unit test that ran successfully in step 7.
   Set a breakpoint in one of the test cases, then request NetBeans to
   debug that class. The class will start running, and then will stop as
   it hits your breakpoint. You should be able to display variables, then
   navigate the call stack to open the source files of each method. You
   should also be able to step through the code. Use the continue icon
   to resume execution. When the test completes, you should see the same
   jUnit test result panel as in step 7 (Note: this feature does not
   currently work).

9. You can also use your Tomcat NetBeans Free-Form project to debug an
   external Tomcat instance that is executing on the same, or a different
   machine. (Obviously, the external instance must be running the same
   version of the source code!)

   The external Tomcat instance must be started with its jvm enabled for
   debugging by adding extra arguments to JAVA_OPTS, e.g.
   -Xdebug -Xrunjdwp:transport=dt_socket,address=8000,server=y,suspend=n

   To debug the external Tomcat instance under NetBeans, select the
   "attach debugger" choice from the debug menu. Accept the default
   JPDA debugger with the SocketAttach connector and the dt_socket
   transport. Specify the hostname and port where the Tomcat jvm is
   listening. Your NetBeans workbench should then connect to the external
   Tomcat and display the running threads. You can now set breakpoints and
   begin debugging.
