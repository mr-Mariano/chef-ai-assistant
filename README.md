# Chef AI Assistant

A recipe suggestion application that turns a list of ingredients into a Markdown recipe. It pairs a React/TypeScript interface with a small Express backend that keeps the inference token on the server.

## Request flow

```text
Ingredient list -> React client -> Express POST /api/anthropic
                                -> Hugging Face Inference -> Markdown recipe
```

Despite the route name, the current backend uses Hugging Face's `InferenceClient`, the `novita` provider, and `deepseek-ai/DeepSeek-V3-0324`. It does not call Anthropic. Provider access and model availability depend on your Hugging Face account and may change.

## Run locally

Prerequisites: Node.js 22.13+, npm, and a Hugging Face token with permission and provider access for the model configured in `backend/index.js`. Inference may require provider credits.

```bash
git clone https://github.com/mr-Mariano/chef-ai-assistant.git
cd chef-ai-assistant
npm ci
cd backend
npm ci
```

Create `backend/.env`:

```dotenv
PORT=5030
ANTROPHIC_HF_TOKEN=replace-with-your-hugging-face-token
```

The spelling `ANTROPHIC_HF_TOKEN` matches the existing source. This must remain a server-side secret.

From `backend/`, start the API:

```bash
npm start
```

In a second terminal, create `.env.local` in the repository root:

```dotenv
VITE_API_URL=http://localhost:5030/api/anthropic
```

This value must include the entire route, not just the server origin: `GetRecipe.tsx` posts directly to it. Values prefixed with `VITE_` are exposed to the browser; put only the API URL here.

From the repository root:

```bash
npm run dev
```

Open the URL printed by Vite. Add ingredients and request a recipe. Restart Vite after changing its environment variables.

## Development commands and structure

```bash
npm run build
npm run lint
npm run preview
```

The frontend build runs TypeScript project checks followed by Vite. A deployed frontend needs `VITE_API_URL` set at build time and a separately running backend.

- `src/components/Ingredients.tsx` and `IngredientInput.tsx`: ingredient collection.
- `src/components/GetRecipe.tsx`: request and loading state.
- `src/components/Recipe.tsx`: Markdown rendering with react-markdown and remark-gfm.
- `backend/index.js`: Express route, system prompt, provider request, and error response.

Stack: React 19, TypeScript, Vite, Tailwind CSS, Radix UI components, Express, and the Hugging Face inference SDK.

## My contribution and provenance

The public history records my application delivery, API-call changes, Markdown rendering improvements, and CSS fixes. Examples: [API request](https://github.com/mr-Mariano/chef-ai-assistant/commit/d1d9432c15) and [Markdown rendering](https://github.com/mr-Mariano/chef-ai-assistant/commit/7fcbaad88c). All five commits on the inspected main branch are authored as Mariano; this records repository activity rather than proving that every underlying design or scaffold is original.

The frontend uses the React/TypeScript/Vite scaffold and Radix-based UI components. The repository does not identify a specific course or tutorial origin. Framework, component, and dependency credits remain with their respective authors.

## Limits and verification

This is an inference integration prototype. The current API has no authentication, rate limiting, or substantive input validation. The frontend request path lacks robust failed-request handling, so a provider error can leave an incomplete recipe or loading state. There is no recipe database or nutritional/allergen validation.

For a manual check, start both servers, add several ingredients, request a recipe, and verify Markdown formatting. The backend's `npm test` is a failing placeholder, and no frontend test script is declared. No hosted demo is documented in this README.
