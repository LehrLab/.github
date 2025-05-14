# Frontend гайд
Цей гайд є обов'язковим до ознайомлення, оскільки він дасть відповіді на більшість питань та іноді навіть більше! Тут ти прочитаєш про перелік бібліотек, інструментарій, що для чого, яка архітектура та як працювати з нею і трошечки інших штучок.

# Бібліотеки
- Основні залежності (продакшн)
`react (18.3.1)` - основна фронтенд бібліотека  
`sass-embedded (1.86.3)` - щоб писати стилі на sass  
`zustand (5.0.3)` - стейт менеджер  
`axios (1.8.4)` - для запитів до беку — навколо нього написана фабрика екземплярів хттп класів  
`@react-oauth/google (0.12.1)` - бібліотека, яка дозволяє юзати авторизацію через Google  

- Інструменти розробки та збірки
`@vitejs/plugin-react-swc (3.5.0)` - збірка, написана на Rust, яка працює швидше, ніж аналог на JavaScript  
`eslint (9.13.0)` - і так все ясно  
`typescript (5.6.2)` - пишемо тільки на ньому  
`typescript-eslint (8.11.0)` - лінт для TypeScript  
`vite (5.4.10)` - основний збірник (аналог Webpack)  
`storybook (8.6.12)` - дозволяє побачити всі стани UI-компонентів та швидко їх дослідити  
 - Node 22.11.0. Раджу юзати `nvm` щоб легко змінювати версії.

# Огляд структури проєкту

[Дошка на міро](https://miro.com/app/board/uXjVNWfhJz0=/)

# Хочу написати запити на бек. Шо робити?
1. В директорії модуля знайди або створи папку services
2. Створи файл module-name.service.ts
3. Клас буде виглядати наступним чином:

```ts
// Імпорт фабрики сервісів
import { httpFactoryService } from '../../../shared/services/http-factory.service';
// Імпорт сервісу.
// Якщо не треба надсилати заголовки Authorization, обирай HttpService,
// інакше EnhancedWithAuthHttpService
import { HttpService } from '../../../shared/services/http.service';

// Тут типи. Групуємо імпорти по тому що імпортуємо - класи окремо, типи окремо і так далі.
import { SomethingRequest, SomethingResponse } from '../types/login.types';

class ModuleNameService {
  // Обов'язково треба написати такий конструктор.
  constructor(private readonly httpService: HttpService) {
    this.httpService = httpService;
  }
	// Усі апі запити асинхроні. Якщо будемо юзати метод класа ззовні, то пишемо модифікатор public, інакше private. Можна нічого не писати і це неявний public але пишіть явно.
	// Паблік методи вгорі, приватні внизу.
	// smthData типізуємо як SomethingRequest. Те, що повертає функція, це SomethingResponse в дженеріку Promise.
  public async helloWorld(smthData: SomethingRequest): Promise<SomethingResponse> {
	  // Зверніть увагу, що в дженеріки методу post, put, patch спочатку йде тип респонсу, потім тип реквесту.
    return this.httpService.post<SomethingResponse, SomethingRequest>('backend.com/api/v1/hello-world/', smthData);
  }
}

// Далі експортуємо ЕКЗЕМПЛЯР класу, не сам клас!
// В конструктор класу передаємо ЕКЗЕМПЛЯР класу HttpService (або іншого, це вже шо тобі треба).
export const moduleNameService = new ModuleNameService(httpFactoryService.createHttpService());
// Вітаю! Ти тільки що написав(ла) свій АРІ клас, заюзавши ООП та патерн "композиція" (погугли шо воно таке).
// Типи для класу створюй в папці src/moduleName/types. Твій сервіс лежить в src/moduleName/services якщо що :)
```
