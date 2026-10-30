# ForCV: карта интервью-материалов

Этот файл помогает быстро находить актуальные материалы и их источники. Связанные Google Docs — канонические версии. Локальные копии в `CV Presentations` должны всегда быть синхронизированы с Google Docs и вложениями Space 1: источник истины — Google, локальные файлы — его экспорт.

## Актуальные материалы из Google Chat Space 1

Space 1: `spaces/AAQATiF1ux8`.

| Материал | Локальная копия | Канонический Google Doc |
| --- | --- | --- |
| CV v8 | `CV Presentations/Yakau_N_AQA_CV_v8.docx` | [Yakau_N_AQA_CV_v8](https://docs.google.com/document/d/16CrGzethQy2tM4XsSFgKZ0gpJtp_m1GQtJlqb-DHnII/edit?usp=drivesdk) |
| Самопрезентация V2 | `CV Presentations/V2 Самопрезентация Yakau N. — Java QA Automation Engineer.docx` | [V2 Самопрезентация](https://docs.google.com/document/d/1SfxxRu4IPUR9ZaZlinLk7U4xbu6S7n-rlEmfPCrBIro/edit?usp=drivesdk) |
| Легенда для интервью, подробная | `CV Presentations/Yakau N. — легенда для интервью Java AQA.docx` | [Подробная легенда](https://docs.google.com/document/d/1cceBP3k3t-d6I9nXtBNTNwF5AFeDLpH2WGTk5SvxUOA/edit?usp=drivesdk) |
| Легенда, простая версия | `CV Presentations/Легенда — простая версия.docx` | [Простая легенда](https://docs.google.com/document/d/12Lw1wJDNDTwPFBf788I0dOt0WauRNPf_UU1rmZN_dhI/edit?usp=drivesdk) |
| 33 вопроса и ответы RU/EN | `CV Presentations/33 вопроса — развёрнутые ответы (RU-EN).docx` | [33 вопроса — ответы](https://docs.google.com/document/d/1QUql4pSrPGDaN2ETsasDgK7Gs6aM7qW4Ppn2eitJaOg/edit?usp=drivesdk) |
| Поля подготовки к интервью | `CV Presentations/interview-prep-fields.md` | Вложение Google Chat в сообщении `spaces/AAQATiF1ux8/messages/RDsq_ioW_Vw.RDsq_ioW_Vw`; локальная копия сверена с вложением по SHA-256. |

## Синхронизация (обязательно)

Все документы Space 1 и их локальные копии должны совпадать. Перед тем как опираться на материал или править его, проверь актуальность и при расхождении обнови локальную копию:

1. Сравни `Modified` Google Doc в Drive с датой локального файла (`search_drive_files`).
2. Если документ новее — переэкспортируй его в DOCX под тем же локальным именем (`get_drive_file_download_url`, `export_format="docx"`).
3. Вложения Google Chat скачивай через `download_chat_attachment`.
4. Google Docs, приложенные в чате, через Chat API не качаются (HTTP 400) — экспортируй их из Drive по ID.
5. После обновления сверь текст DOCX (не байты: zip-метаданные всегда разные) или SHA-256 для бинарных файлов и зафиксируй результат в разделе «Состояние локальных копий».
6. Новые документы, появляющиеся в Space 1, сразу добавляй в таблицу выше и скачивай в `CV Presentations`.

Ограничение: CV в Google Drive (`Yakau_N_AQA_CV_v8`, ID `16CrGzethQy2tM4XsSFgKZ0gpJtp_m1GQtJlqb-DHnII`) пока не трогать — только чтение и экспорт; не менять документ и не заливать в него локальные правки.

## Вспомогательные исходники в корне ForCV

- `Yakau_N_self_presentation_v1.md`
- `Yakau_N_project_legend_v1.md`, `Yakau_N_project_legend_v2.md`, `Yakau_N_project_legend_v3.md`
- `Yakau_N_interview.md`, `Yakau_N_interview_EN.md`
- `Yakau_N_base_cv.md`
- `Yakau_N_AQA_CV_v7.docx` — более ранняя версия CV; для актуального CV используй Google Doc v8 выше.

## Текущая практика интервью

Вопросы для интервью и отработанные варианты ответов собирай и пополняй в одном файле: `CV Presentations/Interview Practice - Questions and Answers.md`.

## Состояние локальных копий

- 29 октября 2026 года проведена сверка Space 1 с локальными копиями: новых сообщений и вложений в Space 1 нет (последнее — 21 октября).
- Текст DOCX сверен с текущими экспортами Google Docs: «33 вопроса», «Легенда — простая версия», «Легенда для интервью (подробная)» совпадают полностью.
- `interview-prep-fields.md` совпадает с вложением Space 1 по SHA-256 (`f4ea5717…c5ba`).
- `Yakau_N_AQA_CV_v8.docx` актуален: текст совпадает с текущим экспортом Google Doc (документ изменён 22 октября, локальная копия — 25 октября).
- Расхождение: в локальном `V2 Самопрезентация Yakau N. — Java QA Automation Engineer.docx` есть пометки `Old` и `NEW`, которых нет в Google Doc. Это единственное отличие; решение о перезаписи не принято — файл сохранён как есть.
- Расхождение: в Space 1 сообщение «CV» ссылается на другой документ — `Yakau Naliahach` (ID `1xNrbXPfzLweDGtHOBeE9pVU9t0BB2zyVpzUeDe4SDos`, изменён 26 октября Mikita Yakunin), а не на канонический `Yakau_N_AQA_CV_v8`. По указанию CV в Drive не трогаем, поэтому эта копия не скачивалась.
- 25 октября 2026 года пять DOCX-файлов в `CV Presentations` заново экспортированы из связанных Google Docs выше.
- Предыдущие локальные версии сохранены в `tmp/cv_before_refresh_cv_refresh_20261025_5ljnpx1/` (на 29 октября папки в vault нет).
