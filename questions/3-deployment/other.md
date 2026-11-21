# Інше

**Домен 3 — Розгортання** · питань: 1  
Теорія: [README.md](../../README.md) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t07-q49"></a>
### T07-Q49
*домен: офіційна мітка*

A company follows collaborative development practices. The engineering manager wants to isolate the development effort by setting up simulations of API components owned by various development teams.

Which API integration type is best suited for this requirement?

- ▫️ HTTP_PROXY
- ✅ MOCK
- ▫️ AWS_PROXY
- ▫️ HTTP

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «MOCK»**

MOCK-інтеграція повертає відповідь, не передаючи запит у бекенд. Де її використовують:
- **тестування API** — інтеграцію перевіряють, не платячи за бекенд;
- **командна розробка** — частини API інших команд замінюють заглушками;
- **CORS** — повернення CORS-заголовків; консоль API Gateway саме так створює метод OPTIONS для CORS.

**❌ Неправильні варіанти:**
- *AWS_PROXY* — Lambda proxy-інтеграція: запит передається у функцію як є.
- *HTTP_PROXY* — запит і відповідь передаються до HTTP-бекенду без змін; integration request і response не налаштовують.
- *HTTP* — інтеграція з HTTP-бекендом, де integration request і response налаштовують вручну через мапінги.

</details>

---
