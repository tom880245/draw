# 網址 https://aog.makie.org/stable/generated/penguins/#Machine-Learning
import Pkg; Pkg.add(["AlgebraOfGraphics", "CairoMakie", "DataFrames", "LIBSVM", "PalmerPenguins"])

因為版本(V1.10)問題 必須用一個包來排除

1.clone 倉庫
git clone https://github.com/andyferris/Dictionaries.jl.git
cd Dictionaries.jl
gh pr checkout 140

2.activate版本
import Pkg;Pkg.activate(".");Pkg.instantiate() 

#Frequency plots

using AlgebraOfGraphics, CairoMakie
penguins = dropmissing(DataFrame(PalmerPenguins.load()))
set_aog_theme!()

axis = (width = 225, height = 225)
penguin_frequency = data(penguins) * frequency() * mapping(:species)

draw(penguin_frequency; axis = axis)


# saving the plot

fg = draw(penguin_frequency; axis = axis)
save("figure.png", fg, px_per_unit = 3)


# 將color 分開 dodge or stack
plt = penguin_frequency * mapping(color = :island, dodge = :island)
draw(plt; axis = axis

或
plt = penguin_frequency * mapping(color = :island, stack = :island)
draw(plt; axis = axis)



###Correlating two variables ,axis = (width = 225, height = 225)

penguin_bill = data(penguins) * mapping(:bill_length_mm, :bill_depth_mm)
draw(penguin_bill; axis = axis)


penguin_bill = data(penguins) * mapping(
    :bill_length_mm => (t -> t / 10) => "bill length (cm)",
    :bill_depth_mm => (t -> t / 10) => "bill depth (cm)",
)
draw(penguin_bill; axis = axis)

加入顏色
plt = penguin_bill * mapping(color = :species)
draw(plt; axis = axis)

加入線條
plt = penguin_bill * linear() * mapping(color = :species)
draw(plt; axis = axis)

線條+散布圖


layers = linear() + mapping()
plt = penguin_bill * layers * mapping(color = :species)
draw(plt; axis = axis)
多加標籤
layers = linear() + mapping(marker = :sex)
plt = penguin_bill * layers * mapping(color = :species)
draw(plt; axis = axis)
分成多張圖 col
layers = linear() + mapping(col = :sex)
plt = penguin_bill * layers * mapping(color = :species)
draw(plt; axis = axis)

擬合線不同
layers = linear() + mapping()
plt = penguin_bill * layers * mapping(color = :species, col = :sex)
draw(plt; axis = axis)



# 密度density圖層Smooth density plots
using AlgebraOfGraphics: density
plt = penguin_bill * density(npoints=50) * mapping(col = :species)
draw(plt; axis = axis)

#colormap = :grayC 指定了使用灰度色图
plt *= visual(colormap = :grayC, colorrange = (0, 6))
draw(plt; axis = axis)


#3D
axis = (type = Axis3, width = 300, height = 300)
layer = density() * visual(Wireframe, linewidth=0.05)
plt = penguin_bill * layer * mapping(color = :species)
draw(plt; axis = axis)

#等高線
axis = (width = 225, height = 225)
layer = density() * visual(Contour)
plt = penguin_bill * layer * mapping(color = :species)
draw(plt; axis = axis)

#混和    
layers = density() * visual(Contour) + linear() + mapping()
plt = penguin_bill * layers * mapping(color = :species)
draw(plt; axis = axis)

#透明度
layers = density() * visual(Contour) + linear() + visual(alpha = 0.5)
plt = penguin_bill * layers * mapping(color = :species)
draw(plt; axis = axis)


# Correlating three variables
body_mass = :body_mass_g => (t -> t / 1000) => "body mass (kg)"
layers = linear() * mapping(group = :species) + mapping(color = body_mass, marker = :species)
plt = penguin_bill * layers
draw(plt; axis = axis)


axis = (type = Axis3, width = 300, height = 300)
plt = penguin_bill * mapping(body_mass, color = :species)
draw(plt; axis = axis)

plt = penguin_bill * mapping(body_mass, color = :species, layout = :sex)
draw(plt; axis = axis)

##Machine Learning
using LIBSVM, Random

# use approximately 80% of penguins for training
Random.seed!(1234) # for reproducibility
N = nrow(penguins)
train = fill(false, N)
perm = randperm(N)
train_idxs = perm[1:floor(Int, 0.8N)]
train[train_idxs] .= true

# fit model on training data and make predictions on the whole dataset
X = hcat(penguins.bill_length_mm, penguins.bill_depth_mm)
y = penguins.species
model = SVC() # Support-Vector Machine Classifier
fit!(model, X[train, :], y[train])
ŷ = predict(model, X)

# incorporate relevant information in the dataset
penguins.train = train
penguins.predicted_species = ŷ

#Now, we have all the columns we need to evaluate how well our classifier performed.

axis = (width = 225, height = 225)
dataset =:train => renamer(true => "training", false => "testing") => "Dataset"
accuracy = (:species, :predicted_species) => isequal => "accuracy"
plt = data(penguins) *
    expectation() *
    mapping(:species, accuracy) *
    mapping(col = dataset)
draw(plt; axis = axis)


#ML  機械學習  支持向量機

using LIBSVM, Random

# use approximately 80% of penguins for training
Random.seed!(1234) # for reproducibility

N = nrow(penguins)
train = fill(false, N)
#創建N個vector 
perm = randperm(N)
train_idxs = perm[1:floor(Int, 0.8N)]
train[train_idxs] .= true

# fit model on training data and make predictions on the whole dataset
X = hcat(penguins.bill_length_mm, penguins.bill_depth_mm)
y = penguins.species
model = SVC() # Support-Vector Machine Classifier
fit!(model, X[train, :], y[train])
ŷ = predict(model, X)

# incorporate relevant information in the dataset
penguins.train = train
penguins.predicted_species = ŷ

axis = (width = 225, height = 225)
dataset =:train => renamer(true => "training", false => "testing") => "Dataset"
accuracy = (:species, :predicted_species) => isequal => "accuracy"
plt = data(penguins) *
    expectation() *
    mapping(:species, accuracy) *
    mapping(col = dataset)
draw(plt; axis = axis)


prediction = :predicted_species => "predicted species"
datalayer = mapping(color = prediction, row = :species, col = dataset)
plt = penguin_bill * datalayer
draw(plt; axis = axis)

pdflayer = density() * visual(Contour, colormap=Reverse(:grays)) * mapping(group = :species)
layers = pdflayer + datalayer
plt = penguin_bill * layers
draw(plt; axis = axis)

