# ليه LangGraph؟

- Publish: 2027-01-24
- Duration: 35–45 seconds
- ClickUp: https://app.clickup.com/t/123mehfy7eh

## Hook

ليه استخدمت LangGraph بدل Chain بسيطة؟

## Spoken script

لأن بعض المحادثات مش خط مستقيم. السؤال ممكن يكون غامض، يحتاج تصنيف، ثم Retrieval، وبعد الإجابة نكتشف Failure Mode ونرجع نصلّح. في نظام دعم eDariba، صممت Flow من خمس Nodes مع State واضح وCritique Loop، واستخدمت Models مختلفة لمراحل مختلفة. LangGraph كان مناسبًا لأن الانتقالات والـstate جزء من منطق المنتج نفسه، مش مجرد تنفيذ تقني.

## Closing

لو الـworkflow فيه فروع وعودة وذاكرة، Graph غالبًا أوضح من Chain طويلة.

## Caption

Choose orchestration based on workflow complexity.

## Material checklist

- Chain vs graph animation
- Clarify / classify / retrieve / generate / critique nodes
- Loop-back arrow for correction
