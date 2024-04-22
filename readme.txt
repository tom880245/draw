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
