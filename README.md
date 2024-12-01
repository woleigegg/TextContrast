# Text Contrast 🔍

A simple web tool for comparing and analyzing text differences. ✨

## Features

- 📝 Text Comparison
  - Side-by-side text comparison
  - Character count display
  - Difference highlighting
  - Line number reference

- 🎨 User Interface
  - Clean and modern design
  - Dark/Light mode support 🌓
  - Responsive layout
  - Synchronized scrolling

- ⚡ Functionality
  - Clear text buttons
  - Click to locate differences
  - Character addition/removal count

## Tech Stack 🛠️

- ⚡ Vue.js 3
- 🎯 Tailwind CSS
- 🎨 Heroicons
- 🔄 diff-match-patch library

## Getting Started 🚀

### Prerequisites 📋

- Node.js >= 16.0.0
- npm (comes with Node.js) or yarn

#### Installing Prerequisites

##### Installing Node.js

###### Windows
1. Download the Node.js installer from [official Node.js website](https://nodejs.org/)
2. Run the installer (.msi file)
3. Follow the installation wizard
4. Verify installation by opening Command Prompt and running:
   ```bash
   node --version
   npm --version
   ```

###### macOS
Using Homebrew:
```bash
brew install node
```
Or download the macOS installer from [official Node.js website](https://nodejs.org/)

###### Linux
Ubuntu/Debian:
```bash
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs
```

Fedora:
```bash
sudo dnf install nodejs
```

##### Installing Yarn (Optional)
If you prefer using yarn over npm:

###### Windows
```bash
npm install -g yarn
```

###### macOS
```bash
brew install yarn
```

###### Linux
```bash
npm install -g yarn
```

### Installation 💻

1. Clone the repository
```bash
git clone https://github.com/yourusername/contrast.git
cd contrast
```

2. Install dependencies

###### Using npm
```bash
npm install
```

###### Using yarn
```bash
yarn install
```

3. Start development server

###### Using npm
```bash
npm run dev
```

###### Using yarn
```bash
yarn dev
```

4. Build for production

###### Using npm
```bash
npm run build
```

###### Using yarn
```bash
yarn build
```

The development server will typically start at `http://localhost:5173`

## Contributing 🤝

We love your input! We want to make contributing to Text Contrast as easy and transparent as possible, whether it's:

- 🐛 Reporting a bug
- 💡 Discussing the current state of the code
- 🎯 Submitting a fix
- ✨ Proposing new features
- 📖 Improving documentation

### Steps to contribute:

1. Fork the Project 🍴
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'feat: add some amazing feature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request 🚀

### Commit Guidelines 📝

We follow the [Conventional Commits](https://www.conventionalcommits.org/) specification. Some examples:

- `feat: add comparison highlighting`
- `fix: resolve scrolling sync issue`
- `docs: update installation guide`
- `style: format code with prettier`
- `refactor: restructure comparison logic`

## TODO 📝

- 💻 Code Mode
  - Syntax highlighting for different programming languages
  - Support for common code formats
  - Language auto-detection
  - Line numbers in code view
  - Code formatting options

## License 📄

This project is licensed under the GNU Affero General Public License v3.0 (AGPL-3.0) - see the [LICENSE](LICENSE) file for details.