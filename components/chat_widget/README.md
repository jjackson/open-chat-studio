# Open Chat Studio Chat Component

A Web Component built with [Stencil](https://stenciljs.com/) that allows you to add a native chat interface to any web page
that connects directly to the Open Chat Studio (OCS) Chat API.

## Features

- **Native Chat Interface**: No iframe required - renders as native web components
- **Real-time Messaging**: Send and receive messages with typing indicators
- **Responsive Design**: Works on desktop and mobile devices
- **Customizable**: Configurable positioning, styling, and behavior
- **Movable Widget**: Optional draggable widget with edge snapping and position persistence
- **Session Management**: Persistent chat sessions with automatic reconnection
- **Error Handling**: Graceful error handling and recovery

## Getting Started

To try this component out, run:

```bash
npm install
npm start
```

Now load the localhost URL shown in the console in your browser.

**Note**: You will need a valid chatbot ID from your OCS instance. The component uses the following API endpoints:
- `POST /api/chat/start/` - Start new chat session
- `POST /api/chat/{session_id}/message/` - Send messages
- `GET /api/chat/{session_id}/{task_id}/poll/` - Poll for responses
- `GET /api/chat/{session_id}/poll/` - Poll for new messages

To build the component for production, run:

```bash
npm run build
```

To run the unit tests for the components, run:

```bash
npm test
```

## Making Changes

To make changes to the component, you can edit the files in the `src/components/open-chat-studio-widget` directory. You can
also edit the `src/index.html` file to change the page that is loaded when you run `npm start`.

### Styling

The component uses [Tailwind CSS](https://tailwindcss.com/) with [DaisyUI](https://daisyui.com/) for styling.

## Movable Widget (Experimental)

The chat widget can be made movable/draggable via opt-in configuration. All options are optional and default to preserving the current fixed-position behavior.

### Draggable Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `enable-dragging` | `boolean` | `false` | Enable dragging/moving the chat widget |
| `drag-axis` | `'both' \| 'x' \| 'y'` | `'both'` | Restrict dragging to specific axis |
| `initial-position` | `string \| object` | `'bottom-right'` | Initial position. Can be corner name (`'bottom-right'`, `'bottom-left'`, `'top-right'`, `'top-left'`) or coordinates (`{ x: number, y: number }`) |
| `safe-margin` | `number \| object` | `12` | Safe margin from viewport edges in pixels. Can be a number or object with `top`, `right`, `bottom`, `left` properties |
| `snap-to-edges` | `boolean` | `true` | Automatically snap to nearest edge when drag ends |
| `snap-threshold` | `number` | `32` | Distance in pixels from edge to trigger snap (only when `snap-to-edges` is true) |
| `z-index-override` | `number` | `undefined` | Override the z-index of the chat widget |
| `position-persistence` | `object` | `undefined` | Custom persistence adapter with `get()` and `set(pos)` methods for saving/loading position |

### Draggable Events

| Event | Detail | Description |
|-------|--------|-------------|
| `positionChange` | `{ x: number, y: number }` | Fired when widget position changes |

### Basic Example

```html
<open-chat-studio-widget
  chatbot-id="your-chatbot-id"
  enable-dragging="true"
  initial-position="bottom-left"
  snap-to-edges="true">
</open-chat-studio-widget>
```

### Advanced Example with Position Persistence

```html
<open-chat-studio-widget
  id="my-chat-widget"
  chatbot-id="your-chatbot-id"
  enable-dragging="true"
  drag-axis="both"
  initial-position='{"x": 100, "y": 100}'
  safe-margin="20"
  snap-to-edges="true"
  snap-threshold="40">
</open-chat-studio-widget>

<script>
  const widget = document.getElementById('my-chat-widget');
  
  // Listen to position change events
  widget.addEventListener('positionChange', (event) => {
    console.log('Widget moved to:', event.detail);
  });
  
  // Add position persistence
  widget.positionPersistence = {
    get: () => {
      const saved = localStorage.getItem('chat-widget-position');
      return saved ? JSON.parse(saved) : null;
    },
    set: (pos) => {
      localStorage.setItem('chat-widget-position', JSON.stringify(pos));
    }
  };
</script>
```

### Keyboard Support

When `enable-dragging` is enabled, the drag handle supports keyboard navigation:
- **Arrow keys**: Move widget by 10px
- **Shift + Arrow keys**: Move widget by 50px
- The drag handle is focusable and labeled for screen readers

### Notes

- Position persistence is host-defined via the `positionPersistence` injection, allowing hosts to choose their storage strategy (localStorage, server, etc.)
- When `enable-dragging` is `false` (default), all draggable features are disabled and the widget behaves as before
- Mobile devices (width < 640px) have different dragging behavior
- Fullscreen mode maintains its own dragging logic independent of the `enable-dragging` prop

## Publishing

This widget is published via GitHub actions. To trigger the action:

* Update the version number in `package.json`.
  * `npm version [patch|minor|major]` 
  * Commit and push the changes.
* Tag the repo with `w_v{version number}`.
* Push the changes and the tag to `main`.