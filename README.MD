# GPT-4
## *[github.com/Torantulino/Auto-GPT](https://github.com/Torantulino/Auto-GPT)*
- Первый настолько выстреливший проект, пытающийся сделать GPT-4 автономной. Составляет план действий, делит задачу на подзадачи, создает отдельных агентов (инстансы GPT-4 со своим заточенным на конкретные задачи первичным промптом и контекстом).
- Может использовать GPT3.5 (он же ChatGPT), но результаты объективно похуже.
- Может использовать [pinecone.io](https://www.pinecone.io/) в качестве памяти. Я пока так и не разобрался, в чём прикол vector-based memory, но его активно поюзывают в связке с языковыми моделями, надо изучить, в чём смысл. Сервис платный, есть репорты о выставлении больших счетов, пока обхожу стороной.
- Есть потуги в сторону [Recursive self improvement](https://github.com/Torantulino/Auto-GPT/issues/15)
- Хотят гонять и [другие LLM кроме OpenAI](https://github.com/Torantulino/Auto-GPT/issues/25), интересно в контексте использования локальных моделей вроде LLaMa

## *[github.com/pHaeusler/micro-agent](https://github.com/pHaeusler/micro-agent)*
- Скромная по масштабам и потому довольно понятная реализация агента, способного писать код и рефлексировать.

## *[github.com/rokstrnisa/Robo-GPT](https://github.com/rokstrnisa/Robo-GPT)*
- Автономный GPT-4 runner, примерный аналог Auto-GPT с более читабельной документацией

## *Нерелизнутое/наобещанное*
- Безымянный GPT-4 coding assistant, полностью управляемый голосом. [Довольно ломовейшее видеодемо](https://mobile.twitter.com/mckaywrigley/status/1644034309253394433)
- **ChaosGPT**: Модифицированная версия Auto-GPT, нацеленная на хаотический деструкшн. Лол. [Видео.](https://www.youtube.com/watch?v=g7YJIpkk7KM) Пока ничерта интересного не сделала, кроме странички в твиттере, но потенциально может быть первым интересным кейсом Rogue AI.

# GPT-3.5 / ChatGPT
## *[github.com/microsoft/JARVIS](https://github.com/microsoft/JARVIS)*
### [Онлайн-демо](https://huggingface.co/spaces/microsoft/HuggingGPT), хочет ключи [отсюда](https://platform.openai.com/account/api-keys) и [оттуда](https://huggingface.co/docs/hub/security-tokens).
- Он же HuggingGPT. Амбициозно названная "коллаборативная система", использующая ChatGPT в качестве контроллера, выполняющего таски, подбирая и используя наиболее подходящие LLM-модели с HuggingFace. 

## *Нерелизнутое/наобещанное*
### *MemoryGPT*
- Какой-то разогрев [происходит в твиттуре](https://twitter.com/rikvk01/status/1645062075285135360) прямо пока я это пишу, надо следить. Кода пока нет, ничего нет, есть Beta Tester Sign-up. Задача - ChatGPT с long-term memory. Тоже использует vector-based database, еще один повод поинтересоваться, как это работает, и почему это эффективно (ну или почему это скам.)

# LLaMA и её друзья
## *[llama.cpp](https://github.com/ggerganov/llama.cpp)*
- Проект на плюсах, бодро гоняющий llama и её деривативы на CPU и RAM. Весьма кстати ввиду довольно жирных требований к памяти у моделей 33B и больше, консюмерские видюхи здесь не вывозят.
## *[alpaca.cpp](https://github.com/antimatter15/alpaca.cpp)*
- Форк llama.cpp, заточенный гонять конкретно Alpaca.

# Open Assistant (LAION)
## *[github.com/LAION-AI/Open-Assistant](https://github.com/LAION-AI/Open-Assistant)*
- Надо пощупать.

# TODO: разобраться, что это и зачем
- [github.com/hwchase17/langchain](https://github.com/hwchase17/langchain)

# Особенности психологии LLM
- В отличие от людей, модель вообще не в курсе о своих способностях и ограничениях. Из чего выводы:
- - Модель не знает о своих ограничениях по длине контекста, из-за чего по ходу выполнения может съезжать с рельсов, когда такие важные  вводные данные, как исходно поставленная задача, из контекста вылетают. Мои рекомендации по данному поводу - вешать менеджмент контекста на отдельного LLM-агента, который вызывается на каждом цикле, задача которого - суммировать, сжимать и вычищать из контекста наименее важные данные, вроде истории исправленных ошибок, повторённых шагов, содержимого уже не актуальных файлов и страниц, замечаний остальных агентов по поводу уже пройденных шагов.
- - Необходимость делить задачи на подзадачи должна быть достаточно подробно вписана в промпты "высокоуровневых" агентов, отвечающих за делегацию задач. Если этого недостаточно, может понадобиться ещё один агент, который знает о зонах ответственности других модулей, и будет страшно материться на делегирующего агента за то, что оный в модуль, например отвечающий за Web Requests и ожидающий URL в качестве инпута, чтобы стянуть страницу с веба, отправляет сразу весь пирог в виде "залезь в надёжные источники и добудь мне новости о ChatGPT за последнюю неделю".
- - Рефлексия и конкуренция в целом критически важны. В идеале, 1 вызов модели - это 1 мысль/1 действие, которое может быть проанализировано и отрефлексировано в следующих вызовах и другими агентами, настороженно относящимися к происходящему. Если в пределах одного вызова попросить модель сформулировать задачу и сразу же оценить правильность её формулировки и реальную полезность в достижении цели, она напридумает 1000 и 1 причину, по которой она молодец и всё правильно делает. Но тут опять же надо учитывать, что эти диалоги модели с самой собой генерируют много мусора, большую часть которого из контекста можно выкидывать после исполнения шага.

# Медиа
- [24 Mar 2023, Reflecting on Reflexion](https://nanothoughts.substack.com/p/reflecting-on-reflexion) - пост с кучей ссылок, довольно дотошно рассказывающий о том, как рефлексия (значительно) улучшает результаты GPT-4 (в бенчмарке [HumanEval](https://paperswithcode.com/dataset/humaneval)), и как её имплементировать.
- YT-канал [@TwoMinutePapers](https://www.youtube.com/@TwoMinutePapers) на фоне потока пейперов не мог не запрыгнуть на поезд и сейчас вываливает обзоры на бумажки по LLM(в основном GPT), можно покрутить на фоне ежели читать лень.
- [7 Apr 2023, Understanding Vector Databases: Powering the World of AI](https://www.linkedin.com/pulse/understanding-vector-databases-powering-world-ai-chris-dougherty) - статья о векторных ДБ, пока не читал, но, возможно, ответит на некоторые вопросы.
# Пэйперы
- [25 Oct 2022, Large Language Models can self-improve, Google](https://arxiv.org/pdf/2210.11610.pdf)
- [20 Dec 2022, Self-Instruct: Aligning Language Model with Self Generated Instructions](https://arxiv.org/pdf/2212.10560.pdf)
- [27 Feb 2023, LLaMA: Open and Efficient Foundation Language Models, Facebook](https://arxiv.org/pdf/2302.13971.pdf)
- [9 Mar 2023, On the Risks of Stealing the Decoding Algorithms of Language Models](https://arxiv.org/pdf/2303.04729.pdf)
- [22 Mar 2023, SparseGPT: Massive Language Models Can be Accurately Pruned in One-Shot](https://arxiv.org/pdf/2301.00774.pdf)
- [24 Mar 2023, GPT-4 Technical Report, OpenAI](https://arxiv.org/pdf/2303.08774.pdf)
- [27 Mar 2023, Sparks of Artificial General Intelligence: Early experiments with GPT-4, Microsoft](https://arxiv.org/pdf/2303.08774.pdf)
- [29 Mar 2023, TaskMatrix.AI: Completing Tasks by Connecting Foundation Models with Millions of APIs, Microsoft](https://arxiv.org/pdf/2303.16434.pdf)
- [30 Mar 2023, DERA: Enhancing Large Language Model Completions with Dialog-Enabled Resolving Agents, Curai](https://arxiv.org/pdf/2303.17071.pdf)
- [30 Mar 2023, Self-Refine: Iterative Refinement With Self-Feedback, NVIDIA, Google](https://arxiv.org/pdf/2303.17651.pdf)
- [30 Mar 2023, Language Models can Solve Computer Tasks](https://arxiv.org/pdf/2303.17491.pdf)
- [2 Apr 2023, HuggingGPT: Solving AI Tasks with ChatGPT and its Friends in Hugging Face, Microsoft](https://arxiv.org/pdf/2303.17580.pdf)
- [3 Apr 2023, Neural Theory-of-Mind: On the Limits of Social Intelligence in Large LMs](https://arxiv.org/pdf/2210.13312.pdf)
- [4 Apr 2023, Summary of ChatGPT/GPT-4 Research and Perspective Towards the Future of Large Language Models](https://arxiv.org/pdf/2304.01852.pdf)
- [6 Apr 2023, Instruction Tuning with GPT-4, Microsoft](https://arxiv.org/pdf/2304.03277.pdf)
- [6 Apr 2023, Do the Rewards Justify the Means? Measuring Trade-Offs Between Rewards and Ethical Behavior in the MACHIAVELLI Benchmark](https://arxiv.org/pdf/2304.03279.pdf)

# Notes
- Проекты - один экспериментальнее другого, и вместе с изучением сорцов у каждого, особенно в случае каких-то затыков, стоит проверять Issues, Pull requests и Discussions - как правило, любой вопрос там как минимум уже подняли.
- Самое главное золото каждого из проектов, которое надо изучать в сорцах - это промпты и работа с памятью/контекстом. Понимание принципов составления эффективных промптов - это основа вообще всего, а в текущих условиях, когда максимальное количество токенов в контексте порядком ограничено даже в самых навороченных моделях и может быть легко превышено засоввыванием в контекст одной html-странички - приходится креативно изгаляться, чтобы модель не забывала, о чем вообще шла речь. К тому же, чем короче контекст, тем дешевле и быстрее запрос.

# P.S.
**Sci-Fi Author**: In my book I invented the Torment Nexus as a cautionary tale

**Tech Company**: At long last, we have created the *Torment Nexus* from classic sci-fi novel *Don't Create The Torment Nexus*
