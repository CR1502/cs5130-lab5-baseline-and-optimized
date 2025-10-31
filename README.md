# Lab 1 Reference Implementation: Image Mosaic Generator

## ⚠️ IMPORTANT NOTICE - FOR CS 5130 STUDENTS ONLY

This is a **reference implementation** provided for students who:
- Did not complete Lab 1
- Have non-functional Lab 1 code
- Need a working baseline for Lab 5

**This code is intentionally simple and INEFFICIENT.** It uses nested loops and suboptimal algorithms. Your job in Lab 5 is to profile this code, identify bottlenecks, and optimize it!

**Note:** Using your own Lab 1 code is preferred if it works. This is a fallback option.

---

## 📋 What This Implementation Does

Creates an image mosaic by:
1. Dividing an input image into a grid
2. Computing the average color of each grid cell
3. Replacing each cell with the best-matching colored tile
4. Providing a Gradio interface for user interaction

---

## 🚀 Getting Started

### Prerequisites

- Python 3.8 or higher
- pip (Python package manager)

### Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/lab1-reference-implementation.git
cd lab1-reference-implementation
```

### Install Dependencies

```bash
pip install -r requirements.txt
```

Or install manually:

```bash
pip install numpy pillow gradio
```

### Run the Application

```bash
python mosaic_generator.py
```

This will:
1. Create a `tiles/` directory with sample colored tiles if it doesn't exist
2. Launch a Gradio interface at `http://localhost:7860`
3. Open your browser automatically

---

## 💻 Usage

### Using the Interface

1. **Upload an image** - Any image format (JPG, PNG, etc.)
2. **Adjust grid size** - Use the slider (8×8 to 64×64)
3. **Click "Generate Mosaic"** - Wait for processing
4. **View results** - See the mosaic output and MSE metric

### Command Line Testing

For quick testing without the UI:

```python
from mosaic_generator import SimpleMosaicGenerator
from PIL import Image

# Initialize generator
generator = SimpleMosaicGenerator(tile_directory='tiles', grid_size=(32, 32))

# Load and process image
input_image = Image.open('your_image.jpg')
mosaic = generator.create_mosaic(input_image)

# Save result
output_image = Image.fromarray(mosaic)
output_image.save('output_mosaic.jpg')
```

---

## 📁 Repository Structure

```
lab1-reference-implementation/
├── README.md                 # This file
├── requirements.txt          # Python dependencies
├── mosaic_generator.py       # Main implementation
├── .gitignore               # Git ignore rules
└── tiles/                   # Tile images (created automatically)
    ├── tile_00.png
    ├── tile_01.png
    └── ...
```

---

## 🐌 Known Inefficiencies (To Fix in Lab 5!)

This implementation has several intentional performance problems that you'll optimize in Lab 5:

### Inefficiency #1: Nested Loops for Grid Division
**Location:** `divide_into_grid()` method  
**Problem:** Using nested loops to extract grid cells
```python
for i in range(grid_h):
    for j in range(grid_w):
        cell = image[start:end, ...]
```
**Impact:** O(n²) operations when it could be vectorized  
**Lab 5 Goal:** Use NumPy array reshaping and slicing

---

### Inefficiency #2: Manual Color Averaging
**Location:** `compute_average_color()` method  
**Problem:** Computing average color with nested loops
```python
for i in range(height):
    for j in range(width):
        sum_r += cell[i, j, 0]
```
**Impact:** Extremely slow for large cells  
**Lab 5 Goal:** Use `np.mean()` for instant computation

---

### Inefficiency #3: Sequential Tile Matching
**Location:** `find_best_tile()` method  
**Problem:** Comparing to each tile one at a time
```python
for i, tile_color in enumerate(self.tile_colors):
    distance = compute_distance(...)
```
**Impact:** Could be vectorized for massive speedup  
**Lab 5 Goal:** Use vectorized distance calculations

---

### Inefficiency #4: Repeated Tile Resizing
**Location:** `create_mosaic()` method  
**Problem:** Resizing tiles during mosaic creation
```python
tile_resized = Image.fromarray(tile).resize(...)
```
**Impact:** Expensive operation done repeatedly  
**Lab 5 Goal:** Pre-compute and cache resized tiles

---

## 📊 Expected Performance (Baseline)

With a 512×512 image and 32×32 grid:

| Metric | Value |
|--------|-------|
| **Processing Time** | ~30-60 seconds |
| **Memory Usage** | Moderate (~200-300 MB) |
| **Main Bottlenecks** | Grid operations, color averaging, tile matching |

**Your Lab 5 Goal:** Achieve **20-100× speedup!**

---

## 🧪 Testing

### Test with Different Settings

```python
# Small image, small grid (fast baseline)
Image: 256×256, Grid: 16×16
Expected time: ~5-10 seconds

# Medium image, medium grid (moderate)
Image: 512×512, Grid: 32×32
Expected time: ~30-60 seconds

# Large image, large grid (slow - good for dramatic optimization)
Image: 1024×1024, Grid: 64×64
Expected time: ~2-5 minutes
```

### Adding Custom Tiles

1. Create or download square images (any size - they'll be resized to 32×32)
2. Place them in the `tiles/` directory
3. Supported formats: JPG, PNG, BMP
4. Restart the application
5. Your custom tiles will be loaded automatically!

**Pro Tip:** Use tiles with distinct colors for better mosaic effects.

---

## 🚢 Deployment

### Local Testing with Gradio

The default setup launches a local server at `http://localhost:7860`

### Deploy to Hugging Face Spaces

1. Create an account at [Hugging Face](https://huggingface.co/)
2. Create a new Space
3. Upload these files:
   - Rename `mosaic_generator.py` → `app.py`
   - Upload `requirements.txt`
   - Optionally upload sample images
4. The Space will automatically build and deploy
5. Share the public URL with others!

**Deployment Guide:** [https://huggingface.co/docs/hub/spaces](https://huggingface.co/docs/hub/spaces)

---

## 🔍 For Lab 5: What to Profile

When you start Lab 5, focus profiling on these critical areas:

### Priority 1: Most Time-Consuming
1. **`divide_into_grid()`** - Grid cell extraction
2. **`compute_average_color()`** - Color averaging
3. **`find_best_tile()`** - Tile matching

### Priority 2: Memory & I/O
4. **`create_mosaic()`** - Overall orchestration
5. **Tile loading and caching** - Initial setup

### Profiling Commands

```python
# Using cProfile
import cProfile
cProfile.run('generator.create_mosaic(image)', sort='cumulative')

# Using line_profiler (after installation)
%load_ext line_profiler
%lprun -f generator.divide_into_grid generator.create_mosaic(image)
```

Use these tools to identify where time is actually spent, then optimize!

---

## ❓ FAQ

### Q: Why is this code so slow?
**A:** It's intentionally inefficient for educational purposes. Real-world code shouldn't use nested loops for operations that can be vectorized. You'll learn to identify and fix these issues in Lab 5!

### Q: Can I use this for my Lab 1 submission?
**A:** No, this is only for students who need a baseline for Lab 5. Lab 1 submissions have already closed.

### Q: The tiles look too simple. Can I add better ones?
**A:** Yes! Add any square images to the `tiles/` folder. They'll automatically be resized to 32×32 and used.

### Q: What if I want to use my own Lab 1 code instead?
**A:** That's preferred! This reference implementation is only for students who need it. Your own code will be more familiar and meaningful to optimize.

### Q: Does this match all Lab 1 requirements?
**A:** It provides the core functionality. Some optional Lab 1 features (like advanced color quantization) are simplified here to focus on the optimization aspects for Lab 5.

### Q: Why is my mosaic output black?
**A:** This has been fixed in the current version. Make sure you have the latest code. If issues persist, check that:
   - Your input image is valid and loads correctly
   - Tiles are being created/loaded (check console output)
   - Run with debug output enabled to see processing details

### Q: Can I modify this code before starting Lab 5?
**A:** Yes, but be careful! The goal of Lab 5 is to optimize inefficient code. If you fix the inefficiencies now, you'll have less to optimize later. We recommend using it as-is for the baseline measurements.

### Q: How do I know if my Lab 5 optimizations are working?
**A:** 
1. Profile before and after optimization
2. Measure exact timing differences
3. Aim for at least 20× speedup (50-100× is typical)
4. Verify output still looks correct!

---

## 🛠️ Troubleshooting

### Issue: "ModuleNotFoundError: No module named 'gradio'"
**Solution:** Run `pip install -r requirements.txt`

### Issue: "No tiles found" message
**Solution:** The app will automatically create sample tiles. Wait a moment and try again.

### Issue: Gradio interface won't open
**Solution:** 
- Check if port 7860 is already in use
- Try: `python mosaic_generator.py --port 7861`
- Check firewall settings

### Issue: Processing takes extremely long
**Solution:** This is expected! The code is intentionally slow. Try:
- Start with smaller images (256×256)
- Use smaller grid sizes (16×16)
- This is what you'll fix in Lab 5!

### Issue: Image appears distorted
**Solution:** 
- Images are resized to square (512×512 by default)
- This is normal behavior for consistent grid division
- You can modify the `target_size` parameter

---

## 📚 Additional Resources

- **NumPy Documentation:** [https://numpy.org/doc/](https://numpy.org/doc/)
- **Gradio Documentation:** [https://gradio.app/docs/](https://gradio.app/docs/)
- **Python Profiling Guide:** [https://docs.python.org/3/library/profile.html](https://docs.python.org/3/library/profile.html)
- **Pillow (PIL) Documentation:** [https://pillow.readthedocs.io/](https://pillow.readthedocs.io/)

### Course Materials
- Week 10: Profiling techniques
- Lab 5: Optimization assignment

---

## 📝 License

This code is provided for educational purposes for CS 5130 students at Northeastern University.

Do not distribute outside the course without permission.

---

## 🤝 Contributing

This is a reference implementation for a specific course assignment. 

If you find bugs:
1. Check if the issue is intentional (part of the inefficiency)
2. Open an issue on GitHub if it's a real bug
3. For course-related questions, contact your instructor or TA

---

## 📧 Contact

**Questions about the code?** Open an issue on GitHub  
**Questions about Lab 5?** Contact your instructor or TA  
**Questions about CS 5130?** Check the course Canvas page

---

## 🎓 Learning Outcomes

By using this reference implementation for Lab 5, you will learn to:

- ✅ Profile real code to identify bottlenecks
- ✅ Apply NumPy vectorization for massive speedups
- ✅ Refactor code into modular, maintainable components
- ✅ Measure and verify performance improvements
- ✅ Write production-quality optimized code

**Remember:** The goal of Lab 5 isn't just to make code faster—it's to learn systematic approaches to analyzing, optimizing, and improving software while maintaining quality.

---

<div align="center">

**Ready to optimize? Get started with Lab 5! 🚀**

⭐ Star this repo if it helped you!

</div>