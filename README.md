# Aula-pratica-AI
Build a robust, fully automated internal AI Slide Design API and backend service inside this application. 

The API must dynamically consume existing lesson plans stored in our database, transform them into sophisticated slide decks, and support custom visual style references (image/URL uploads or layout inspiration) provided by the user.

### 1. API ARCHITECTURE & ENDPOINTS

Create a dedicated backend service/module with the following primary endpoints:

1. POST /api/slides/generate
   - Request Body:
     {
       "lesson_plan_id": "string", // ID of the existing lesson plan in the database
       "style_reference": {
         "reference_image_urls": ["string"], // Optional array of image URLs or layout references
         "theme_preference": "dark | light | minimalist | corporate | vibrant", // Default: Auto-detect
         "color_palette": ["#HEX1", "#HEX2", "#HEX3"], // Optional
         "font_family": "string" // Optional
       },
       "target_slide_count": "number" // Default: Auto-calculate based on lesson duration
     }
   - Logic:
     - Fetch full lesson plan context (Title, Objectives, Warm-up, Core content, Activities, Assessment, BNCC/Standards).
     - Send a structured system prompt to the LLM/Multimodal model (OpenAI GPT-4o / Claude 3.5 Sonnet) including:
       a) The structured lesson content.
       b) Visual analysis of the `style_reference` images (extracting layout hierarchy, typography, contrast, and visual rhythm).
     - Generate a JSON output representing the presentation schema (slide by slide).

2. GET /api/slides/:id
   - Retrieves generated presentation JSON, including metadata, slide layouts, and export states.

3. POST /api/slides/:id/export
   - Exports the slide presentation to downloadable formats: `.pptx`, `.pdf`, or responsive HTML/CSS web slides.

---

### 2. SLIDE DESIGN ENGINE & SCHEMA (JSON OUTPUT STRUCTURE)

Ensure the LLM prompt inside the API outputs a strict JSON structure containing sophisticated design rules for every single slide:

- Slide Types: Cover, Agenda, Single Key Takeaway, 2-Column Comparison, Timeline/Step-by-Step, Quote/Highlight, Interactive Quiz/Question, and Summary.
- Design System Rules:
  - High Contrast & Visual Hierarchy: Emphasize key terms, bold typography, concise bullet points (max 4 lines per slide), and clean margins.
  - Visual Elements: Automatically suggest icon names (Lucide-react icons), image placeholders, or layout grids derived from the provided `style_reference`.
  - Color Roles: Explicitly define `background`, `primary_text`, `accent_color`, and `card_background` for each slide based on the reference theme.

JSON Schema Example to enforce:
{
  "presentation_title": "string",
  "theme": {
    "background": "string",
    "text_color": "string",
    "primary_accent": "string",
    "font_family": "string"
  },
  "slides": [
    {
      "slide_number": 1,
      "layout_type": "title_cover",
      "header": "string",
      "subheader": "string",
      "content": {},
      "visual_notes": "string",
      "icon_suggestions": ["string"]
    }
  ]
}

---

### 3. FRONTEND INTEGRATION & DESIGN REFERENCE UI

Connect this API to a new "Gerar Slides Automáticos" (Generate Automatic Slides) modal/component inside the existing Lesson Plan view:

1. Style Reference Uploader:
   - Allow teachers to upload or paste image URLs of slide designs they like (e.g., Pinterest screenshots, Canva templates, Minimalist slides).
   - Convert uploaded files to base64 or storage URLs and pass them to the API's `reference_image_urls`.

2. One-Click Generation Workflow:
   - Include a primary action button: "🎨 Gerar Apresentação de Slides com IA".
   - Show a modern loading/skeleton animation: "Analisando referências visuais...", "Formatando conteúdo pedagógico...", "Aplicando design de alta performance...".

3. Live Slide Previewer:
   - Render a responsive HTML/React interactive preview of the generated slides using Tailwind CSS.
   - Allow inline text editing and style adjustments (theme color picker, font selector).

4. Export Options:
   - Provide "Baixar PPTX" (PowerPoint editable download using `pptxgenjs` or similar library) and "Baixar PDF".

---

### 4. DEPENDENCIES & SETUP
- Install required NPM packages for presentation rendering and export (e.g., `pptxgenjs`, `jspdf`, `lucide-react`).
- Set up database relationships (e.g., a `slide_decks` table linked to `lesson_plans`).
- Ensure proper error handling, model timeout fallbacks, and API rate limiting.
