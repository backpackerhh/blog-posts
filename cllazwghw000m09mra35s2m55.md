---
title: "(ES) El de mi experiencia en Domestika"
seoDescription: "Mi experiencia en Domestika desde el inicio hasta el final"
datePublished: Mon Aug 14 2023 14:53:57 GMT+0000 (Coordinated Universal Time)
cuid: cllazwghw000m09mra35s2m55
slug: el-de-mi-experiencia-en-domestika
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1691745074046/749327f3-801b-4510-9f8f-b46437696755.png
tags: ere, domestika, despidos, trabajos

---

> This post is also [available in English](https://blog.davidmp.es/the-one-about-my-experience-at-domestika).

Desde hace unos días ya no trabajo oficialmente para [Domestika](https://domestika.org/). Puedo decir sin ninguna duda que, en algunos aspectos, ha sido mi mejor experiencia laboral. Hasta el momento. Sin embargo, me voy con un sabor agridulce y con la certeza de que era lo mejor que podía pasarme en este momento, ya que los últimos meses han sido bastante difíciles y me dejaron un mal sabor de boca.

A continuación, voy a contar mi experiencia en la empresa desde el inicio hasta el final. En ningún caso pretendo ni dejar en mal lugar ni limpiar su imagen, sino contar las cosas desde mi punto de vista.

## Inicios

Me uní a la empresa a mediados de noviembre de 2020. En ese momento había dos equipos en la parte web, _Growth_ y _Learning_, del que yo pasé a formar parte.

Después de unas cuantas semanas en las que hubo unos cuantos movimientos en el equipo, algunos compañeros se fueron y otros llegaron, por un tiempo nos quedamos un tech lead, tres project managers (sí, tres... y llegó a haber una cuarta), tres desarrolladores backend y una desarrolladora frontend.

A mí me contrataron como desarrollador backend, pero puntualmente también cogía tareas de frontend, para aliviar un poco la carga de trabajo que había por esa parte.

Muy pronto pude comprobar que si por algo destacaba Domestika era por la **calidad humana** de las personas que formaban parte de la empresa. Daba la sensación de que era algo que se cuidaba con mucha atención.

Por ejemplo, en el proceso de _onboarding_ se asignaba al nuevo miembro del equipo un _onboarding buddy_ que ayudaría en el proceso y un board con tareas a completar (configurar el proyecto, leer la documentación, etc.), entre las que se encontraba asistir a un _daily stand up_ de los equipos con los que probablemente acabarías colaborando a lo largo del tiempo y presentarte. En mi caso, asistí a la daily del equipo de _Growth_, de _Mobile_, de _Infraestructura_ y de _Diseño_. El objetivo era que te conocieran, que tú les conocieras a ellos y hacerte una idea de cómo trabajaban.

También teníamos un canal de Slack donde opcionalmente podías unirte para que cada cierto tiempo te tocara tener un [donut](https://www.donut.com/) con otra persona que estuviera en ese canal, de forma aleatoria. Acordabas fecha y hora para dedicar 30 minutos a conocer a la otra persona, idealmente sin tocar temas de trabajo.

A nivel técnico me dejó muy buena impresión encontrar en Domestika cosas como:

- Todo PR era revisada y tenía que ser aprobada por al menos un miembro del equipo.

- Todo PR debía incluir los tests apropiados para la funcionalidad añadida o modificada.

- Los tests de cada PR creada se ejecutaban automáticamente en la _integración continua_ (CI) con Jenkins (posteriormente con GitLab).

- Los miembros del equipo (incluido los PMs) hacíamos el QA de las tareas de los demás en base a los criterios definidos.

- En staging se aplicaba _Continuous Deployment_ (CD), mientras que en producción se aplicaba _Continuous Delivery_ (CD).

- Aplicábamos _Scrum_ con sprints de 2 semanas y todas las ceremonias habituales (daily stand up, sprint planning, sprint review, sprint retrospective)

- Si durante los _refinements_ semanales se encontraba alguna tarea que generase dudas sobre cómo completarla, no se estimaba y se dedicaba cierto tiempo para hacer un _spike_.

- Un día de cada sprint estaba por entero dedicado a lo que llamábamos _Bug Monday_, para corregir algunos bugs ocurridos en producción relacionados con cada equipo o para corregir deuda técnica.

- En caso de que ocurriese algún error con relativa importancia en producción escribíamos _post-mortems_ para dar visibilidad al resto de la empresa.

Aunque a alguno pueda resultarle de lo más habitual tener todo eso en su día a día, mi experiencia es que es menos habitual de lo que cabría esperar hoy en día. Varios de mis compañeros me confirmaron posteriormente que también les ocurría lo mismo.

Además de todo eso, mientras estuviéramos disponibles para las reuniones más importantes, disponíamos de horario totalmente flexible. Para mí es tan importante que jamás, mientras pueda permitírmelo, volveré a aceptar un trabajo si no dispongo de esa posibilidad.

Y ya por último, teníamos a nuestra disposición la posibilidad de asistir a clases de inglés una hora a la semana, que no debía incluirse como parte del horario laboral. Una medida que nunca entendí y que, honestamente, nunca cumplí.

Durante este periodo inicial, en el equipo de _Learning_ destacaría el proyecto en el que añadimos nuevos idiomas a la plataforma de Domestika (francés e italiano en ese caso). El equipo se coordinó de maravilla para sacarlo adelante y en unas pocas semanas estaba todo listo. Mejoramos procesos automáticos que existían previamente y añadimos funcionalidades pensando en facilitar la adición de nuevos lenguajes en el futuro.

Sin duda, eran tiempos de bonanza en la empresa. Seguían contratando a gente para unirse a los equipos y eso llevó a cambios.

## Un nuevo rumbo

La plataforma de Domestika está desarrollada en [Ruby on Rails](https://rubyonrails.org/). Es un proyecto que empezó a desarrollarse hace más de una década y ha pasado por las manos de decenas de desarrolladores. Estoy seguro que cada uno de nosotros siempre ha querido crear el mejor código posible, pero la realidad es que enfrentarse a ese proyecto por primera vez puede llegar a ser incluso desmoralizador.

A principios de 2021 se tomó la ambiciosa decisión de desacoplar la plataforma de Domestika. El backend expondría una [API REST](https://developer.mozilla.org/en-US/docs/Glossary/REST) que sería consumida desde el frontend mediante una [Single-Page Application (SPA)](https://developer.mozilla.org/en-US/docs/Glossary/SPA). Para ello, se creó el equipo de _Desacople_, que iría desacoplando página a página la web de Domestika mientras el resto de equipos seguiría añadiendo nuevas funcionalidades y seguiría dando mantenimiento al resto de la web que seguía aún acoplada.

Además, se creó un equipo de _Arquitectura_ que se encargaría principalmente de tomar decisiones sobre cómo afrontar el desacople de la web, definir guías de estilos, mejorar tiempos de carga en la web, mejorar tiempos de ejecución de la suite de tests, etc.

Cabe destacar que, según nos contaron, la intención era que el resto de desarrolladores que no formábamos parte integral del equipo de _Desacople_ fuéramos rotando cada _x_ sprints para colaborar con el desacoplamiento de la web, pero eso nunca llegó a hacerse.

Sin embargo, en etapas posteriores y de manera puntual, para ciertas funcionalidades totalmente nuevas, sí que nos juntábamos con el equipo de _Desacople_ y _Arquitectura_ para acordar cómo hacerlo con el nuevo enfoque de API + SPA.

También se creó el equipo de _Platform_, encargado de gestionar el panel de administración de la plataforma.

Con respecto a los equipos ya existentes, el equipo de _Growth_ en algún momento se dividió en tres equipos y el equipo de _Learning_ se dividió (o expandió, según palabras del _Head of Engineering_) en dos equipos.

Por un lado, el equipo de _Learning Experience_, cuyo proyecto estrella, Domestika Live, estuvo en desarrollo durante casi un año y nunca llegó a ver la luz. Una pena, porque el curro fue tremendo y nunca se les dio la oportunidad de ver su creación en funcionamiento. Desconozco los motivos que llevaron a esa decisión. Después de eso, el equipo fue disuelto y los miembros que quedaron en la empresa se unieron a otros equipos.

Por otro lado, el equipo de _Teaching & Community_, del que yo pasé a formar parte.

Aquí llegó también un cambio importante para mí. Debido a la dificultad que estaba teniendo la empresa para encontrar desarrolladores frontend con las cualidades requeridas y al interés que siempre mostré en hacer tareas de frontend, me ofrecieron la posibilidad de cambiar de rol. Eso implicaba que en lugar de ser un desarrollador backend que puntualmente también tocaba frontend, pasaría a ser un desarrollador frontend que puntualmente también tocaba backend.

De primeras no estaba seguro del cambio, pero el _architect lead_ me convenció mostrándome cómo sería la parte de desacople del frontend y que toda la plataforma iría en esa dirección. No obstante, la realidad es que nunca llegó a pasar eso y que me tocó lidiar con un frontend en el monolito de Rails donde reinaba el caos. Nunca estaba seguro de dónde poner cada cosa, principalmente ante la ausencia de una documentación y/o guía de estilo claramente definida. Eso implicaba tener que preguntar constantemente a algunos compañeros con mayor contexto de esa parte del proyecto.

Así que con ese cambio de rol, en el equipo de _Teaching & Community_ nos quedamos un tech lead, un project manager, un desarrollador backend y un desarrollador frontend.

Personalmente, considero esa etapa como mi mejor etapa en la empresa, a pesar de comerme algún marrón como rehacer el editor de proyectos de Domestika durante casi dos meses. Había muy buen ambiente entre nosotros y éramos muy ágiles sacando el trabajo adelante.

Ni siquiera el cambio de tech lead (el anterior se fue a liderar al equipo de _Arquitectura_) hizo mella en el ambiente o en nuestro ritmo.

Y el otoño de 2021 nos trajo un regalo en forma de proyecto.

## Reproductor de vídeo personalizado

Al final del verano de 2021 se detectó un problema de seguridad con Wistia, uno de los proveedores de vídeos de los cursos, que estaba exponiendo por error unas credenciales que permitían a los usuarios descargar los vídeos y sobreescribir los subtítulos.

Una vez corregido ese problema, se decidió que la mejor opción era crear un **reproductor de vídeo** personalizado para Domestika. De esa manera tendríamos control absoluto sobre la apariencia del reproductor, los audios, los subtítulos y los thumbnails, mientras que los proveedores solo seguirían proporcionando el vídeo. Gestionar la codificación de los vídeos, sus diferentes resoluciones y demás tareas relacionadas se consideró que era inasumible en esa etapa inicial.

Nos informaron del nuevo proyecto y prácticamente de un día para otro se creó el equipo del _Video Player_ para empezar a trabajar inmediatamente. Dicho equipo estaba formado por los equipos de _Desacople_, _Arquitectura_ y _Teaching & Community_.

Este nuevo proyecto supuso un nuevo cambio de rol para mí. Para que hubiese el mismo número de desarrolladores de backend que de frontend volví a desempeñar la labor de desarrollador backend. Inicialmente me molestó que lo decidieran sin preguntarme antes, sobre todo porque sabía que ese proyecto iba a estar totalmente desacoplado en el lado del frontend y me hubiera gustado tener la oportunidad de trabajar de esa manera, especialmente después de llevar un tiempo trabajando con el frontend del monolito.

Planteé mi malestar y me dijeron que podría coger tareas de frontend también, si así lo deseaba. Y así fue al principio. Sin embargo, el trabajo que teníamos que hacer en el backend fue mucho más interesante y laborioso de lo que yo había anticipado, así que mi foco estuvo casi por completo en el backend durante los 6 meses que aproximadamente estuvimos trabajando en ese proyecto.

Durante ese tiempo tuve la oportunidad de liderar la arquitectura del nuevo reproductor de vídeo en el backend. Entre muchas otras cosas, estuve a cargo de tareas tan importantes como la generación de los archivos **m3u8** que consume el propio reproductor para reproducir los vídeos y la orquestación para servir esos archivos de forma estática.

A día de hoy, si me preguntan en un proceso de selección cuál es el proyecto o pieza de código de la que me siento más orgulloso, sin duda nombraría lo que hice en este proyecto.

En esta etapa los sprints no estaban tan bien planificados como anteriormente, pero a cambio teníamos mayor flexibilidad para pivotar en caso necesario.

Por otra parte, ya en el plano personal, cuando estaba a punto de cumplir mi primer año en la empresa empecé a preguntar acerca de una subida de sueldo, que sabía me había ganado con creces. Por desgracia, nunca hubo transparencia en temas de salarios en Domestika. No sabía cuál era la política de la empresa, si aplicaban las subidas a todos juntos en determinado momento del año, al cumplir tu aniversario en la empresa... A pesar de todo, me subieron el sueldo sin poner ninguna pega.

A principios de 2022 se anunció que [Domestika se convertía en unicornio](https://www.businessinsider.es/domestika-nuevo-unicornio-sangre-espanola-1002237) al levantar una ronda de financiación de casi 100 millones de euros y estar valorada en más de 1.000 millones.

La empresa seguía aumentando el número de países donde estaba presente y el número de empleados seguía creciendo sin parar, llegando a superar los 800. Sin embargo, algo ya empezó a cambiar durante ese mismo año 2022.

## Nuevos proyectos

En primavera de 2022 ya habíamos terminado el proyecto del nuevo reproductor de vídeo, pero los usuarios solo pudieron disfrutarlo en los trailers de los cursos, donde teníamos a Vimeo como proveedor. Por lo menos hasta final de ese año no pudimos dar acceso a todos los usuarios en las lecciones de los cursos debido a problemas en la codificación de los vídeos de Wistia, que provocaban saltos automáticos de resolución mientras veían los vídeos.

Normalmente el problema ocurría con vídeos antiguos que internamente Wistia había codificado de forma diferente y la mayoría de las veces bastaba con resubir los vídeos. Sin embargo, en contadas ocasiones eso no solucionaba el problema y nos costó un tiempo afinar lo suficiente hasta encontrar la solución definitiva.

Al terminar ese proyecto, el equipo del _Video Player_ se disolvió y cada equipo volvió a sus quehaceres habituales. En el caso de _Teaching & Community_ pasamos a ser simplemente _Teaching_.

Poco después de eso, salió la [noticia](https://www.epe.es/es/activos/20220428/domestika-tecnologica-despidos-ere-encubierto-13576187) de que algunos antiguos empleados de Domestika habían denunciado a la empresa por un supuesto ERE encubierto.

Ahí saltaron un poco las alarmas entre los que estábamos en la empresa, pero siempre se nos aseguró en reuniones All-Hands que todo iba bien, especialmente en la parte de ingeniería. Como se seguía contratando a más y más desarrolladores, parecía razonable pensar que era cierto.

Además, en verano me subieron el sueldo de nuevo, pero esta vez sin yo pedirlo. Alguien me comentó por ese entonces que la empresa quería evitar la fuga de talento.

Después del nuevo reproductor de vídeo continuamos con un proyecto fallido que intentaba introducir una nueva suscripción para los usuarios de la plataforma. Siempre se nos aseguró que debíamos hacerlo lo más rápido posible, pero que si funcionaba tendríamos la posibilidad de tomarnos el tiempo necesario para hacerlo bien. Nunca me lo creí, así que en cierta manera me alegré cuando nos anunciaron que la nueva suscripción no había funcionado, porque estaba convencido de que no nos hubieran permitido rehacerlo por completo.

Posteriormente, nuestro project manager nos presentó el proyecto más ambicioso al que nos habíamos enfrentado hasta la fecha, el **teacher dashboard**, que quería mejorar la experiencia de los profesores en la plataforma.

Estaba previsto que dedicáramos en torno a dos años a ese proyecto, por lo que el equipo prácticamente se duplicó en cuestión de unos meses, pasando a estar formado por un tech lead, un project manager, cuatro desarrolladores backend, cuatro desarrolladores frontend y una diseñadora de UI/UX.

En mi caso, me quedé definitivamente en el rol de desarrollador backend, colaborando cada vez menos en tareas de frontend.

Y no era para menos. El teacher dashboard nos dio la oportunidad de empezar un proyecto desde cero. En el caso del frontend estaba claro que tendríamos una SPA, como se estaba haciendo para las páginas desacopladas. Sin embargo, en el backend las páginas desacopladas usaban una API REST cuyos endpoints se habían desarrollado con un código mejor organizado y más limpio, pero todavía acoplado al monolito de Rails.

Aquí llegaron muchas reuniones con los equipos de _Desacople_ y _Arquitectura_ para acordar entre todos cómo íbamos a hacerlo. La decisión final fue usar los [engines de Rails](https://guides.rubyonrails.org/v6.1/engines.html), apoyándonos en el enfoque propuesto por Shopify con [Upgrow](https://github.com/backpackerhh/upgrow-docs). Dicho enfoque ya había servido de inspiración para el desarrollo de los endpoints de la API REST, pero nosotros quisimos ser más estrictos, limitando lo máximo posible el acoplamiento con Rails e intentando aplicar [Domain-Driven Design (DDD)](https://martinfowler.com/bliki/DomainDrivenDesign.html) de una manera muy laxa.

Partiendo de una prueba de concepto inicial, vimos que parecía factible hacer lo que queríamos con los engines. Aún así, me sorprendió la poca documentación que encontré online relativa a casos de uso realmente complejos en producción.

Esa etapa la disfruté bastante porque pude liderar una vez más el desarrollo de un proyecto tan importante para la empresa, que además sentaría las bases de lo que haríamos en el resto de la aplicación en el backend. Hubo mucha prueba y error, pero fue un aprendizaje continuo. Siempre con la inestimable ayuda de mis compañeros, por supuesto.

Algunos highlights fueron la gema extraída del monolito conteniendo el código reutilizable para los engines o el engine de autenticación usado tanto en la API REST como en GraphQL.

Por supuesto, no todo fue un camino de rosas. Tuvimos carta blanca para hacerlo lo mejor posible mientras no encontramos obstáculos, pero en el mismo momento en que encontramos alguna dificultad al extraer ciertas funcionalidades desde el monolito a un engine, la apuesta por los engines ya no estaba tan clara. Llegaron incluso a decirnos que el desarrollo de los engines se paraba, pero conseguí que pudiéramos seguir añadiendo nuevas funcionalidades a los engines. Por suerte, casi todo lo relacionado con el teacher dashboard era nuevo, al igual que lo era el siguiente proyecto que llegó a mis manos.

A finales del verano de 2022, los usuarios que tuvieran una suscripción Domestika Plus, tanto mensual como anual, tendrían acceso a un catálogo de 1200 cursos, seleccionados según una variedad de reglas de negocio definidas por la gente de producto. Entre otras muchas reglas, por ejemplo, un curso solo puede estar disponible en el catálogo durante tres meses y una vez que sale tiene que pasar al menos un mes para volver a estar disponible.

Fue otro de esos proyectos que disfruté de principio a fin, donde tuve libertad creativa a la hora de desarrollar el código. Además, fue un proyecto en el que colaboré muy estrechamente con una desarrolladora junior a la que había estado mentorizando en los meses anteriores.

Viendo la apuesta tan grande por parte de la empresa en este proyecto, que se esperaba que fuera un _game changer_, desde luego nadie esperaba lo que vendría a continuación.

En algún momento de ese año (honestamente no recuerdo cuándo), nos reunieron a todos los empleados de Domestika en un All-Hands y nos comunicaron que cerraban todos los estudios de grabación, excepto los de Madrid. Por lo tanto, se veían afectados los trabajadores de Latinoamérica, Italia, Francia, Alemania, Reino Unido y Estados Unidos. Lo peor de todo fue que toda esa gente se enteró que perdía su trabajo en ese preciso momento, junto al resto de trabajadores de la empresa.

Nos explicaron que esa decisión se había tomado porque les salía más rentable pagarle los gastos a cada profesor para que viajase de cualquier lugar del mundo a Madrid que mantener abierto un estudio de grabación en esos otros países.

Recuerdo hablar de ese tema posteriormente con el resto de mi equipo y a nosotros nos sorprendió la decisión, aunque pudiera tener cierta lógica si habían calculado los costes de una y otra opción, pero sobre todo nos sorprendió la forma de notificarlo.

Por lo demás, nos dijeron de nuevo que todo iba bien en la empresa y que los trabajadores en España no teníamos que preocuparnos, porque esa decisión no nos afectaba.

Y así nos siguieron diciendo en todos y cada uno de los All-Hands a los que nos convocaron en los meses siguientes. Incluso en el último, poco antes de que todo cambiase definitivamente.

## El principio del fin

Antes de continuar hay que comentar un pequeño detalle para entender mejor lo que viene a continuación. Según la información sacada de [esta noticia](https://www.epe.es/es/activos/20220428/domestika-tecnologica-despidos-ere-encubierto-13576187) (ya enlazada anteriormente), Domestika tiene varias filiales, dos de ellas en España: **DMSTK** y **Estudios de Grabación Digital**.

Pues bien, el 22 de febrero de 2023 recibimos un email en el buzón de nuestro correo de empresa. Ahí nos informaban sobre la intención de la empresa de hacer un ERE en _DMSTK_ que afectaría a 89 empleados, filial para la que yo trabajaba en ese momento.

Ese mismo día se creó una nueva cuenta de Slack donde podíamos ir hablando sobre el proceso. Para los temas más críticos se crearon canales privados donde se pudiese garantizar que nadie de las "altas esferas" de la compañía se infiltrase. El primer paso era elegir a los representantes de los trabajadores, hasta un máximo de 3 por cada centro de trabajo, que eran Barcelona y Madrid. Para ello tendríamos 15 días.

Luego tuvimos una reunión con Colectivo Ronda, que ya había llevado el caso del supuesto ERE encubierto del año anterior, y decidimos que ellos eran la opción más obvia para que nos representaran. Una vez pasados los 15 días establecidos por ley para elegir a los representantes de los trabajadores, se iniciaría el periodo de negociaciones con los abogados de la empresa, que duraría 30 días.

Desde ese momento en que recibimos el email de la empresa nada volvió a ser igual. En mi equipo nadie tenía experiencia en EREs, así que la incertidumbre era máxima. Cada reunión que teníamos giraba en torno al ERE, como era de esperar. Pero esas semanas que pasaron desde que nos informaron del ERE hasta que se hizo efectivo se hicieron interminables para todos, especialmente para aquellos que estaban en la lista de afectados.

Tal y como yo lo veo, durante ese tiempo podías tomarte el asunto de dos formas. O bien no tenías ganas de trabajar (opción más que razonable) o usabas el trabajo como válvula de escape para no pensar demasiado en la posibilidad de que fueran a echarte. Personalmente, soy de los del segundo grupo. Por eso seguí trabajando en un sistema recomendador de cursos, que ya había empezado unas semanas antes.

Empezó el periodo de negociaciones y el primer día ya se dio a conocer el listado de afectados, aunque obviamente no se hizo público. Cada trabajador tenía que contactar con los abogados por email y ellos nos dirían si estábamos en la lista o no. En mi caso, no estaba incluido.

La empresa alegaba no tener los beneficios suficientes para poder seguir manteniendo a todos los empleados, aunque ni los abogados ni gente de finanzas afectados por el ERE estaban de acuerdo basándose en los datos que tenían disponibles. Se rumoreaba ya por ese entonces que la intención era vender, pero aún hoy parece que no se ha concretado nada, si esa es realmente su intención.

Ya había empezado el periodo de negociaciones cuando se filtró a la prensa que [Domestika iba a hacer un ERE para sustituir a los empleados por IA](https://www.businessinsider.es/auge-caida-domestika-ludismo-siglo-xxi-1230310). Y como suele pasar en estos casos, esa noticia se hizo viral. Me gustaría aprovechar estas líneas para contar la verdad sobre esa historia.

En la noticia se menciona que [ChatGPT](https://openai.com/blog/chatgpt) será el encargado del trabajo de traducción de ahora en adelante para Domestika. Y ahí es donde creo que puedo arrojar algo de luz al tema.

Domestika da soporte a 8 idiomas en el momento en el que estoy escribiendo estas líneas. Entre otras labores que seguramente desconozca, el equipo de traducción se encargaba de traducir manualmente los subtítulos de los trailers de los cursos en cada uno de los idiomas para asegurar la mejor calidad posible de los textos, además de los contenidos de cada curso en su idioma original. Durante años, para el resto de idiomas se ha usado una traducción automática proporcionada por **Google Cloud Translate**, específicamente el [SDK desarrollado por Google para Ruby](https://github.com/googleapis/google-cloud-ruby/blob/main/google-cloud-translate/README.md). Otras partes, como los foros públicos y el foro de cada curso, se traducen igualmente de forma automática.

Sí es cierto es que en determinado momento nos pidieron que esas partes que se traducían manualmente pasasen a traducirse de forma automática, como pasaba con el resto. En ningún caso con ChatGPT.

Creo que era importante aclarar ese detalle. Dicho eso, continuo con la historia.

Una vez finalizado el periodo de negociaciones, los abogados consiguieron una indemnización de 33 días por año trabajado (por ley son 20 días), entre otras cosas. Básicamente, era como que la empresa reconociese un despido improcedente.

Finalmente, el ERE se hizo efectivo el 19 de abril y ese mismo día se informó a los trabajadores de la filial _Estudios de Grabación Digital_ de la intención de hacer un ERE que afectaría a 89 empleados. Otra vez la misma cantidad de despidos.

Ante la falta de transparencia por parte de la empresa acerca de lo que vendría en el futuro una vez terminado todo el proceso, muchos compañeros no incluidos en la lista de afectados del ERE decidieron irse voluntariamente teniendo ya un puesto de trabajo garantizado en otra empresa. En mi caso, decidí quedarme y ver qué deparaba ese incierto futuro.

Según nos comentó posteriormente el _VP of Engineering_, debido a esas bajas voluntarias, habían tenido que cambiar de planes y rehacer los equipos que tenían planteados inicialmente. Eso suponía que en lugar de seguir con el plan de seguir desacoplando la web poco a poco, se entraría en **modo mantenimiento** del monolito. No habría nuevos desarrollos, sino que nos limitaríamos a asegurarnos de que nada se rompía.

A partir de ese momento volvió a haber dos equipos en la parte web, _Ecommerce_ y _Learning_, del que yo pasé a formar parte nuevamente.

Poco trabajo hicimos durante los meses siguientes. Nos reuníamos cada mañana en el _daily stand up_ para hablar de nuestras vidas y compartir nuestros planes de futuro. Los que estaban en procesos de selección con otras empresas compartían sus impresiones y sus avances. Fue una etapa de conocernos mejor, especialmente con aquellos que estaban en otros equipos y fueron asignados al nuestro.

Hasta que un día, el CEO de la empresa, que no había dado la cara cuando se anunció el ERE, tuvo el descaro de pedirnos a través de un canal de Slack que diéramos la bienvenida a dos nuevos empleados de la empresa que ocuparían cargos de responsables del área de negocios y tecnología (este último junto al _VP of Engineering_ actual). Estas dos personas eran cofundadores de una plataforma de cursos "en vivo" operando en la India, según sus propias palabras. Por supuesto, absolutamente nadie reaccionó a ese mensaje.

La gente estaba bastante enfadada por toda esa situación, porque además de la teoría de la venta de la empresa, también se rumoreaba la posibilidad de que nos quisieran sustituir por mano de obra más barata, concretamente de la India. Y ese mensaje no hacía presagiar nada bueno.

Unas semanas después, el 9 de junio de 2023 (un viernes a las 13:00 horas), mismo día en el que se hacía efectivo el ERE en la filial de _Estudios de Grabación Digital_, recibimos otro email en el buzón de nuestro correo de empresa. Ahí nos informaban sobre la intención de la empresa de hacer un nuevo ERE en _DMSTK_, pero esta vez no se especificaba cuántos empleados se verían afectados.

Y el proceso volvió a repetirse. Elegimos a los representantes de los trabajadores y empezó el periodo de negociación estipulado por ley, solo que en la práctica esta vez fue algo más corto que anteriormente. Los abogados nos dieron esa posibilidad al estar las negociaciones de EREs anteriores tan recientes y con la casi total seguridad de que la empresa ofrecería las mismas condiciones.

Pocos días después de anunciar el nuevo ERE, se filtró la siguiente imagen de Glassdoor:

![Ofertas de trabajo en la India](https://user-images.githubusercontent.com/685978/259871808-e930e9e9-710a-4729-887b-83b7f18bb724.jpeg align="left")

Los salarios están en rupias, que convertidos a euros van desde los 4K hasta los 10K aproximadamente. Se confirmaba entonces que esos rumores de buscar mano de obra barata eran ciertos.

Debió darles vergüenza, porque no tardaron en retirar esas ofertas de Glassdoor una vez se supo que esa imagen se había filtrado.

Con respecto al equipo, aunque habíamos comentado la posibilidad de que hubiera un nuevo ERE, supongo que no esperábamos que ocurriera tan pronto. En cualquier caso, fue la confirmación de que Domestika parecía no tener un rumbo definido. En nuestro día a día, seguimos con la misma rutina comentada anteriormente. Nos reuníamos para hablar sobre nosotros mismos, pero no había tareas que hacer.

Viendo el panorama, decidí hablar con el _VP of Engineering_ para pedirle que hiciera todo lo posible para incluirme en la lista de afectados de este ERE. Me dijo que entendía que quisiera irme y que haría lo posible por incluirme en la lista. La mayoría de los miembros de mi equipo, si no todos, hicieron lo mismo y, afortunadamente, todos fuimos incluidos.

Nos sorprendió también que un miembro del equipo de Mobile, incluido en la lista de afectados por el ERE, fue contactado por un desarrollador de la India directamente por LinkedIn para decirle que iban a ser compañeros en Domestika. No hubo ningún tipo de notificación al respecto por parte de la empresa.

Al terminar el periodo de negociaciones, con 46 empleados afectados, nuestros abogados acordaron con la empresa la misma indemnización que en EREs anteriores y un permiso retribuido desde el 10 al 31 de julio de 2023. Por lo tanto, no tenía que volver a trabajar para Domestika.

En el momento de irme, en la parte web quedaban un tech lead, un desarrollador backend y dos desarrolladores frontend. Ningún project manager y creo que ningún diseñador UI/UX.

Para no variar, el mismo día que se hacía efectivo nuestro ERE se informó a los trabajadores de la filial _Estudios de Grabación Digital_ de la intención de hacer un nuevo ERE.

Por si fuera poco, parece que ya están echando a gente de la India también:

![Desarrolladores de la India aparentemente están siendo despedidos también](https://user-images.githubusercontent.com/685978/257259670-3c85078d-79fe-4660-a380-bbf8fcbf2093.png align="left")

Con todo lo que ha hecho durante este año, creo que Domestika está cosechando una imagen de la que difícilmente va a poder recuperarse. No hay más que ver las [opiniones en Glassdoor](https://www.glassdoor.com/Reviews/Domestika-Reviews-E749272.htm).

Había muchas cosas buenas, pero poco o nada quedará ya de eso.

## Despedida

Lo que siento al irme es tristeza, por lo que fue y por cómo acabó.

La gente fue lo mejor de esta experiencia, sin lugar a dudas.

Le deseo lo mejor a los que se quedan, especialmente a aquellos con los que he trabajado más directamente durante estos dos años y medio.

Le deseo lo mejor también y le doy las gracias a todos los compañeros que tuve durante este tiempo.

Ojalá lo que esté por venir para mí sea al menos igual de enriquecedor que mi experiencia en Domestika.
