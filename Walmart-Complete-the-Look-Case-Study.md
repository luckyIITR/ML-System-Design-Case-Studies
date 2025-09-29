# Walmart Complete the Look: ML System Design Case Study

## What is Complete the Look (CTL)?

Walmart's **Complete the Look (CTL)** is a personalized recommendation system that generates complete, stylized outfits built around a single item a customer is viewing. Instead of just showing similar items, it creates entire coordinated looks that help customers envision complete outfits.

**Key Stats:**
- Generates complete outfits from 4-5 distinct product types
- Uses 1,000+ product types and super product types (SPTs)
- Leverages visual similarity and style embeddings
- Covers Fashion and Home categories
- Processes millions of product combinations

## The Core Problem

### Why Complete Outfit Recommendation is Hard

1. **Style Compatibility**: Items must visually and stylistically complement each other
2. **Scale**: Walmart has millions of products across thousands of categories
3. **Personalization**: Different customers have different style preferences
4. **Coherence**: All items in an outfit must work together as a cohesive look
5. **Coverage**: Need to generate looks for items with limited interaction data
6. **Real-time**: Must generate recommendations quickly for user experience

### The Business Impact
- **Improved Discovery**: Customers find complete outfits instead of individual items
- **Higher Conversion**: Seeing complete looks increases purchase confidence
- **Basket Size**: Single-item journeys become multi-item purchases
- **User Experience**: Saves time and provides style inspiration
- **Revenue**: Higher average order value and customer satisfaction

## System Architecture Overview

### Two Main Algorithms

**1. Algorithmic Look Generation**
- Creates looks from scratch using ML models
- Uses complementary item recommendations
- Applies style matching and visual coherence
- Handles cold start and new items

**2. Look Coverage Expansion**
- Expands human-curated looks to similar items
- Uses visual search to find compatible items
- Scales merchant/stylist expertise across catalog
- Ensures consistency and quality

## Algorithmic Look Generation (5-Step Process)

### Step 1: Candidate Selection
**Goal**: Identify items that are complementary and compatible with the anchor item

**Input Sources**:
- **Complementary Item (CI) Model**: Uses co-purchase, co-view, customer engagement
- **Cold Start Models**: For items with limited interaction data
- **Multi-objective Ranking**: Balances multiple recommendation signals

**Filtering Process**:
- **Age/Gender Filters**: Ensure items match customer demographics
- **Seasonal Filters**: Provide seasonally appropriate items
- **Blacklist Filters**: Remove inappropriate or controversial items

### Step 2: Look Definition
**Goal**: Define what constitutes a complete look for different product types

**Product Type Hierarchy**:
- **Product Types (PTs)**: Specific categories (t-shirts, jeans, jackets)
- **Super Product Types (SPTs)**: Grouped similar PTs (all rainwear types)
- **Look Definition**: 4-5 distinct SPTs that form a complete outfit

**Example Look Definition**:
```
Anchor: Shirt
Look Components: Pants + Jacket + Shoes + Accessories
```

**Dynamic Adjustment**:
- User feedback adjusts look definitions
- Low-engagement looks get lower ranking or removal
- Continuous optimization based on customer interactions

### Step 3: Look Generation
**Goal**: Create multiple complete outfits from the defined look components

**Process**:
1. Filter complementary recommendations by SPT groups
2. Select multiple items per SPT
3. Generate raw looks with multiple options
4. Handle missing SPTs using style embeddings

**Example**:
```
Anchor: Orange Dress
Generated Look: 
- Shoes: Heels, Sneakers, Boots
- Accessories: Handbag, Jewelry
- Outerwear: Cardigan, Blazer
```

### Step 4: Outfit Style Matching
**Goal**: Ensure visual coherence and style compatibility within outfits

**Triplet Learning Approach**:
- **Input**: (Anchor, Positive Item, Negative Item)
- **Training**: Learn style compatibility relationships
- **Output**: Style embedding scores for visual similarity

**Training Data Sources**:
- **Merchant-curated outfits**: Professional stylist combinations
- **Customer engagement**: Clicks, add-to-cart, purchases from CTL module
- **Triplet Generation**: Positive from same outfit, negative from different outfit

**Technical Implementation**:
- **CLIP Image Embeddings**: Pre-trained visual features
- **Feed-forward Network**: Triplet loss training
- **Style Embeddings**: Visual similarity scoring

**Image Selection Pipeline**:
- **Problem**: Product images vary in quality and context
- **Solution**: 2-step pipeline to select best images
  1. Identify images with human models
  2. Classify and select "laid-down" images (no human, clean background)
- **Result**: Better embeddings from cleaner product images

### Step 5: Look Ranking & Variant Expansion
**Goal**: Rank looks by compatibility and expand to all sizes/variants

**Ranking Factors**:
- **Price Proximity**: Items in similar price ranges
- **Brand Affinity**: Consistent brand preferences
- **Color Compatibility**: Harmonious color combinations
- **Weighted Combination**: User interaction feedback adjusts weights

**Variant Expansion**:
- **Size Expansion**: Same look for all sizes of anchor item
- **Color Matching**: Assign looks to items with same color
- **Coverage Expansion**: Significantly increases model coverage

## Look Coverage Expansion Algorithm

### The Human-Machine Collaboration Problem
**Challenge**: 
- **Humans**: Great at creating new, unseen styles
- **Scale**: Cannot provide styles for millions of items
- **Solution**: Expand human-curated looks using visual search

### Search-and-Refine Algorithm

**Step 1: Visual Search**
- **Input**: Human-curated look with anchor item
- **Process**: Find visually similar items using CLIP embeddings
- **Scale**: Hundreds of similar items per anchor
- **Tools**: Faiss or ScaNN for approximate nearest neighbor search

**Step 2: Combinatorial Expansion**
- **Process**: Replace each item in original look with similar items
- **Result**: Massive number of new look combinations
- **Challenge**: Not all combinations are coherent

**Step 3: Refine and Filter**
- **Problem**: Visual similarity ≠ style compatibility
- **Solution**: Domain-specific filtering rules
- **Examples**:
  - Queen bed must match queen mattress
  - Adult pants cannot be in kids' look
  - Size, age, gender compatibility checks

### Example Expansion Process
```
Original Look: Black Dress + Heels + Handbag
↓
Visual Search: Find 100 similar black dresses
↓
Combinatorial: 100 × 50 × 30 = 150,000 combinations
↓
Refine: Filter for size/gender/age compatibility
↓
Final: ~10,000 coherent looks
```

## Technical Deep Dive

### Style Embedding Architecture
**Model**: Triplet Learning with CLIP + Feed-forward Network
**Input**: Product images → CLIP embeddings
**Training**: Triplet loss (anchor, positive, negative)
**Output**: Style compatibility scores

### Visual Search Implementation
**Embedding Model**: CLIP (Contrastive Language-Image Pre-training)
**Search Method**: Approximate Nearest Neighbor (Faiss/ScaNN)
**Scale**: Millions of product images
**Speed**: Real-time similarity search

### Product Type Hierarchy
**Level 1**: Product Types (PTs) - Specific categories
**Level 2**: Super Product Types (SPTs) - Grouped similar PTs
**Level 3**: Look Definitions - Complete outfit combinations
**Benefits**: 
- Better recall coverage
- Reduced combination complexity
- Hierarchical style understanding

## Performance Metrics & Results

### Business Metrics
- **Discovery**: Increased product awareness across categories
- **Conversion**: Higher purchase confidence and completion rates
- **Basket Size**: Multi-item purchases from single-item views
- **User Experience**: Time savings and style inspiration

### Technical Metrics
- **Coverage**: Expanded from limited human styles to full catalog
- **Quality**: Style coherence through triplet learning
- **Speed**: Real-time recommendation generation
- **Scalability**: Handles millions of product combinations

## Key Lessons Learned

### Lesson 1: Human-Machine Collaboration is Powerful
**Insight**: Combine human creativity with machine scalability
**Implementation**: 
- Use human-curated styles as foundation
- Scale through visual search and refinement
- Maintain quality through domain-specific filters

**Result**: Best of both worlds - creativity + scale

### Lesson 2: Visual Similarity ≠ Style Compatibility
**Problem**: Items that look similar might not be stylistically compatible
**Solution**: Multi-layered approach
- Visual similarity for initial search
- Style embeddings for compatibility
- Domain rules for final filtering

**Key Insight**: Need multiple signals for complete understanding

### Lesson 3: Image Quality Matters for Embeddings
**Challenge**: Product images vary in quality and context
**Solution**: Automated image selection pipeline
- Identify best product images
- Remove human models and clutter
- Use clean, focused product shots

**Impact**: Better embeddings = better recommendations

### Lesson 4: Hierarchical Product Understanding
**Approach**: Product Types → Super Product Types → Look Definitions
**Benefits**:
- Better coverage than individual categories
- Reduced combination complexity
- More complete outfit understanding

## System Design Interview Points

### Scalability Challenges
1. **Scale**: Millions of products, thousands of categories
2. **Real-time**: Fast recommendation generation
3. **Coverage**: Generate looks for items with limited data
4. **Quality**: Maintain style coherence at scale

### Key Trade-offs Made
1. **Human vs. Machine Generation**
   - **Chose**: Hybrid approach (human creativity + machine scale)
   - **Result**: Quality + coverage

2. **Visual vs. Style Similarity**
   - **Problem**: Visual similarity ≠ style compatibility
   - **Solution**: Multi-layered filtering and refinement
   - **Result**: Better style coherence

3. **Coverage vs. Quality**
   - **Challenge**: Scale human expertise across catalog
   - **Solution**: Search-and-refine with domain rules
   - **Result**: High coverage with maintained quality

### Technical Challenges Solved
1. **Cold Start**: Items with no interaction data
2. **Style Coherence**: Ensuring items work together
3. **Scale**: Millions of product combinations
4. **Real-time**: Fast recommendation generation

### Architecture Decision Rationale
1. **Why Triplet Learning?**
   - Learns relative style relationships
   - Better than absolute similarity
   - Captures complex style interactions

2. **Why CLIP Embeddings?**
   - Pre-trained on diverse visual data
   - Good for product image understanding
   - Efficient for similarity search

3. **Why Hierarchical Product Types?**
   - Better coverage than individual categories
   - Reduced complexity
   - More complete outfit understanding

## Interview Questions You Should Be Ready For

### System Design Questions
1. **"Design a complete outfit recommendation system"**
   - Start with requirements (style, scale, personalization)
   - Discuss product hierarchy and look definition
   - Explain candidate selection and filtering
   - Cover style matching and ranking
   - Address scalability and real-time constraints

2. **"How would you handle cold start for new products?"**
   - Discuss visual similarity approaches
   - Cover human-curated style expansion
   - Explain domain-specific filtering
   - Address quality vs. coverage trade-offs

3. **"How do you ensure style coherence in recommendations?"**
   - Explain triplet learning for style relationships
   - Discuss visual similarity vs. style compatibility
   - Cover multi-layered filtering approaches
   - Address human feedback integration

### Technical Questions
1. **"How do you handle the scale of millions of products?"**
   - Discuss hierarchical product organization
   - Cover approximate nearest neighbor search
   - Explain combinatorial expansion strategies
   - Address real-time performance requirements

2. **"What's the role of visual embeddings in fashion recommendations?"**
   - Explain CLIP embeddings for product images
   - Discuss triplet learning for style relationships
   - Cover image quality and preprocessing
   - Address similarity search implementation

3. **"How do you balance human expertise with machine scalability?"**
   - Discuss human-curated style foundation
   - Explain visual search expansion
   - Cover domain-specific refinement
   - Address quality maintenance at scale

### Architecture Questions
1. **"How do you handle the combinatorial explosion of outfit combinations?"**
   - Discuss hierarchical product types
   - Cover look definition constraints
   - Explain filtering and refinement steps
   - Address quality vs. coverage optimization

2. **"What's your approach to personalization in fashion recommendations?"**
   - Discuss demographic filtering
   - Cover user interaction feedback
   - Explain style preference learning
   - Address individual vs. general style patterns

## Key Takeaways for Interviews

### Technical Insights
1. **Hybrid Human-Machine Approach**: Combine human creativity with machine scalability
2. **Multi-layered Filtering**: Visual similarity + style compatibility + domain rules
3. **Hierarchical Understanding**: Product types → super types → complete looks
4. **Image Quality Matters**: Better embeddings from cleaner product images

### Business Insights
5. **Complete Experience**: Generate full outfits, not just individual items
6. **Style Coherence**: Ensure all items work together visually
7. **Coverage Expansion**: Scale human expertise across full catalog
8. **User Experience**: Save time and provide style inspiration

### System Design Principles
9. **Scalability by Design**: Handle millions of products and combinations
10. **Quality Maintenance**: Ensure style coherence at scale
11. **Real-time Performance**: Fast recommendation generation
12. **Continuous Learning**: Improve with user feedback

---

*This case study demonstrates how Walmart built a sophisticated recommendation system that combines human creativity with machine learning to generate complete, stylized outfits at scale. It's an excellent example of hybrid human-machine systems in e-commerce.*
