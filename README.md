import requests
from PIL import Image, ImageDraw, ImageFont
import io
import os
import random
import hashlib
from datetime import datetime
import matplotlib.pyplot as plt
import numpy as np

class FreeImageGenerator:
    def __init__(self):
        """
        Initialize Free Image Generator
        No API key required - uses free services and local generation
        """
        print("🎨 Free Image Generator initialized successfully!")
        print("Available methods:")
        print("  1. Abstract pattern generation (local)")
        print("  2. Text-based image creation (local)")
        print("  3. Geometric art generation (local)")
        print("  4. Placeholder image generation")

    def generate_abstract_image(self, prompt, size=(1024, 1024), style="colorful"):
        
        try:
            print(f"🎨 Generating abstract image: '{prompt}'")

            image = Image.new('RGB', size, color='white')
            draw = ImageDraw.Draw(image)

            
            colors = self._get_color_palette(prompt, style)

            
            seed = int(hashlib.md5(prompt.encode()).hexdigest()[:8], 16)
            random.seed(seed)
            np.random.seed(seed % 2147483647)

            
            self._draw_abstract_patterns(draw, size, colors, prompt)

            print("✅ Abstract image generated successfully!")
            return image

        except Exception as e:
            print(f"❌ Error generating abstract image: {e}")
            return None

    def _get_color_palette(self, prompt, style):
        
        prompt_lower = prompt.lower()

        
        color_schemes = {
            "colorful": [(255, 87, 87), (87, 255, 87), (87, 87, 255), (255, 255, 87), (255, 87, 255), (87, 255, 255)],
            "minimal": [(50, 50, 50), (100, 100, 100), (150, 150, 150), (200, 200, 200), (230, 230, 230)],
            "dark": [(30, 30, 40), (60, 40, 80), (80, 60, 100), (100, 80, 120), (40, 60, 80)],
            "pastel": [(255, 182, 193), (173, 216, 230), (152, 251, 152), (255, 218, 185), (221, 160, 221)]
        }

        
        color_keywords = {
            "fire": [(255, 69, 0), (255, 140, 0), (255, 215, 0), (220, 20, 60)],
            "water": [(0, 191, 255), (30, 144, 255), (0, 206, 209), (72, 209, 204)],
            "forest": [(34, 139, 34), (107, 142, 35), (85, 107, 47), (154, 205, 50)],
            "sunset": [(255, 94, 77), (255, 154, 0), (255, 206, 84), (255, 238, 173)],
            "space": [(25, 25, 112), (72, 61, 139), (106, 90, 205), (147, 112, 219)],
            "ocean": [(0, 119, 190), (0, 180, 216), (144, 224, 239), (173, 216, 230)]
        }

      
        for keyword, colors in color_keywords.items():
            if keyword in prompt_lower:
                return colors

      
        return color_schemes.get(style, color_schemes["colorful"])

    def _draw_abstract_patterns(self, draw, size, colors, prompt):
        """Draw various abstract patterns"""
        width, height = size
        prompt_lower = prompt.lower()

      
        if "geometric" in prompt_lower or "shapes" in prompt_lower:
            self._draw_geometric_patterns(draw, size, colors)
        elif "flowing" in prompt_lower or "water" in prompt_lower or "waves" in prompt_lower:
            self._draw_flowing_patterns(draw, size, colors)
        elif "spiral" in prompt_lower or "circle" in prompt_lower:
            self._draw_spiral_patterns(draw, size, colors)
        else:
          
            self._draw_mixed_patterns(draw, size, colors)

    def _draw_geometric_patterns(self, draw, size, colors):
      
        width, height = size

       
        for _ in range(20):
            color = random.choice(colors)

            
            x1 = random.randint(0, width // 2)
            y1 = random.randint(0, height // 2)
            x2 = random.randint(x1, width)
            y2 = random.randint(y1, height)
            draw.rectangle([x1, y1, x2, y2], fill=color + (random.randint(100, 200),))

            cx = random.randint(0, width)
            cy = random.randint(0, height)
            radius = random.randint(20, 100)
            draw.ellipse([cx-radius, cy-radius, cx+radius, cy+radius],
                        fill=random.choice(colors) + (random.randint(100, 200),))

    def _draw_flowing_patterns(self, draw, size, colors):
       
        width, height = size

        
        for i in range(10):
            color = random.choice(colors)
            y_offset = i * height // 10

            points = []
            for x in range(0, width, 10):
                y = y_offset + 50 * np.sin(x * 0.01 + i) + random.randint(-20, 20)
                points.append((x, y))

           
            if len(points) > 2:
                points.append((width, height))
                points.append((0, height))
                draw.polygon(points, fill=color + (100,))

    def _draw_spiral_patterns(self, draw, size, colors):
       
        width, height = size
        center_x, center_y = width // 2, height // 2

      
        for i in range(10):
            radius = i * 50 + 20
            color = colors[i % len(colors)]
            draw.ellipse([center_x - radius, center_y - radius,
                         center_x + radius, center_y + radius],
                        outline=color, width=3)

    def _draw_mixed_patterns(self, draw, size, colors):
        
        width, height = size

        # Random lines
        for _ in range(30):
            color = random.choice(colors)
            x1, y1 = random.randint(0, width), random.randint(0, height)
            x2, y2 = random.randint(0, width), random.randint(0, height)
            draw.line([x1, y1, x2, y2], fill=color, width=random.randint(1, 5))

    def generate_text_image(self, text, size=(800, 400), font_size=40, bg_color="white", text_color="black"):
       
        try:
            print(f"📝 Generating text image: '{text}'")

            
            image = Image.new('RGB', size, color=bg_color)
            draw = ImageDraw.Draw(image)

            
            try:
                font = ImageFont.truetype("arial.ttf", font_size)
            except:
                try:
                    font = ImageFont.truetype("DejaVuSans.ttf", font_size)
                except:
                    font = ImageFont.load_default()

            
            bbox = draw.textbbox((0, 0), text, font=font)
            text_width = bbox[2] - bbox[0]
            text_height = bbox[3] - bbox[1]

            x = (size[0] - text_width) // 2
            y = (size[1] - text_height) // 2

            # Draw text
            draw.text((x, y), text, fill=text_color, font=font)

            print("✅ Text image generated successfully!")
            return image

        except Exception as e:
            print(f"❌ Error generating text image: {e}")
            return None

    def generate_placeholder_image(self, width=800, height=600, color=None, text=None):
       
        try:
            if not color:
                color = f"#{random.randint(0, 0xFFFFFF):06x}"

            if not text:
                text = f"{width}x{height}"

            print(f"🖼️  Generating placeholder: {width}x{height}")

            
            image = Image.new('RGB', (width, height), color=color)
            draw = ImageDraw.Draw(image)

      
            try:
                font = ImageFont.truetype("arial.ttf", 36)
            except:
                font = ImageFont.load_default()

            bbox = draw.textbbox((0, 0), text, font=font)
            text_width = bbox[2] - bbox[0]
            text_height = bbox[3] - bbox[1]

            x = (width - text_width) // 2
            y = (height - text_height) // 2

            # Choose contrasting text color
            bg_rgb = Image.new('RGB', (1, 1), color).getpixel((0, 0))
            text_color = "white" if sum(bg_rgb) < 384 else "black"

            draw.text((x, y), text, fill=text_color, font=font)

            print("✅ Placeholder image generated!")
            return image

        except Exception as e:
            print(f"❌ Error generating placeholder: {e}")
            return None

    def generate_matplotlib_art(self, prompt, size=(10, 10), style="colorful"):
        
        try:
            print(f"📊 Generating matplotlib art: '{prompt}'")

            
            fig, ax = plt.subplots(figsize=size, dpi=100)
            ax.set_xlim(-10, 10)
            ax.set_ylim(-10, 10)
            ax.axis('off')

            
            if "spiral" in prompt.lower():
                t = np.linspace(0, 4*np.pi, 1000)
                x = t * np.cos(t)
                y = t * np.sin(t)
                ax.plot(x, y, linewidth=2)
            elif "wave" in prompt.lower():
                x = np.linspace(-10, 10, 1000)
                y1 = np.sin(x)
                y2 = np.cos(x)
                ax.plot(x, y1, linewidth=2, label='sin')
                ax.plot(x, y2, linewidth=2, label='cos')
            else:
                
                x = np.random.randn(1000)
                y = np.random.randn(1000)
                colors = np.random.rand(1000)
                ax.scatter(x, y, c=colors, alpha=0.6, s=50)

            
            buf = io.BytesIO()
            plt.savefig(buf, format='png', bbox_inches='tight', pad_inches=0)
            buf.seek(0)

            
            image = Image.open(buf)
            plt.close(fig)

            print("✅ Matplotlib art generated!")
            return image

        except Exception as e:
            print(f"❌ Error generating matplotlib art: {e}")
            return None

    def save_image(self, image, filename=None, output_dir="generated_images"):
        """Save image to file"""
        if not image:
            print("❌ No image to save!")
            return None

        try:
            os.makedirs(output_dir, exist_ok=True)

            if not filename:
                timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
                filename = f"generated_image_{timestamp}.png"

            if not filename.lower().endswith(('.png', '.jpg', '.jpeg')):
                filename += '.png'

            filepath = os.path.join(output_dir, filename)
            image.save(filepath, quality=95)
            print(f"✅ Image saved: {filepath}")

            return filepath

        except Exception as e:
            print(f"❌ Error saving image: {e}")
            return None

    def show_image(self, image):
        """Display image"""
        if image:
            try:
                image.show()
            except Exception as e:
                print(f"Cannot display image: {e}")
        else:
            print("❌ No image to display!")

def main():
    """Interactive main function"""
    print("🎨 Free Image Generator")
    print("=" * 40)
    print("No API key required! Choose generation method:")
    print("1. Abstract art (based on text prompt)")
    print("2. Text image")
    print("3. Placeholder image")
    print("4. Mathematical art")
    print("")

    generator = FreeImageGenerator()

    while True:
        try:
            print("\n" + "="*50)
            choice = input("Choose method (1-4) or 'quit' to exit: ").strip()

            if choice.lower() in ['quit', 'exit', 'q']:
                print("👋 Goodbye!")
                break

            if choice == '1':
              
                prompt = input("Enter description for abstract art: ").strip()
                if prompt:
                    style = input("Style (colorful/minimal/dark/pastel) [colorful]: ").strip()
                    style = style if style in ['colorful', 'minimal', 'dark', 'pastel'] else 'colorful'

                    image = generator.generate_abstract_image(prompt, style=style)
                    if image:
                        filepath = generator.save_image(image)
                        if input("Display image? (y/n) [y]: ").strip().lower() != 'n':
                            generator.show_image(image)

            elif choice == '2':
              
                text = input("Enter text to display: ").strip()
                if text:
                    bg_color = input("Background color [white]: ").strip() or "white"
                    text_color = input("Text color [black]: ").strip() or "black"

                    image = generator.generate_text_image(text, bg_color=bg_color, text_color=text_color)
                    if image:
                        filepath = generator.save_image(image)
                        if input("Display image? (y/n) [y]: ").strip().lower() != 'n':
                            generator.show_image(image)

            elif choice == '3':
              
                width = int(input("Width [800]: ").strip() or "800")
                height = int(input("Height [600]: ").strip() or "600")

                image = generator.generate_placeholder_image(width, height)
                if image:
                    filepath = generator.save_image(image)
                    if input("Display image? (y/n) [y]: ").strip().lower() != 'n':
                        generator.show_image(image)

            elif choice == '4':
                
                prompt = input("Enter description (try 'spiral', 'wave', or anything): ").strip()
                if prompt:
                    image = generator.generate_matplotlib_art(prompt)
                    if image:
                        filepath = generator.save_image(image)
                        if input("Display image? (y/n) [y]: ").strip().lower() != 'n':
                            generator.show_image(image)

            else:
                print("❌ Invalid choice. Please enter 1-4.")

        except KeyboardInterrupt:
            print("\n👋 Goodbye!")
            break
        except ValueError:
            print("❌ Invalid input. Please try again.")
        except Exception as e:
            print(f"❌ Error: {e}")
def quick_abstract(prompt, style="colorful", filename=None):
    """Quick abstract image generation"""
    generator = FreeImageGenerator()
    image = generator.generate_abstract_image(prompt, style=style)
    if image:
        return generator.save_image(image, filename)
    return None

def quick_text(text, filename=None):
    """Quick text image generation"""
    generator = FreeImageGenerator()
    image = generator.generate_text_image(text)
    if image:
        return generator.save_image(image, filename)
    return None

def quick_placeholder(width=800, height=600, filename=None):
    """Quick placeholder generation"""
    generator = FreeImageGenerator()
    image = generator.generate_placeholder_image(width, height)
    if image:
        return generator.save_image(image, filename)
    return None

if __name__ == "__main__":
    
    main()
