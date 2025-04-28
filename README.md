-- Criando a biblioteca UI
local MyLibrary = {}
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- Configurações da UI
local UIConfig = {
    WindowWidth = 500,
    WindowHeight = 350,
    WindowColor = Color3.fromRGB(30, 30, 30),
    AccentColor = Color3.fromRGB(0, 170, 255),
    TextColor = Color3.fromRGB(255, 255, 255),
    Font = Enum.Font.SourceSansBold,
    FontSize = Enum.FontSize.Size14,
    CornerRadius = UDim.new(0, 4),
    ElementHeight = 35,
    ElementPadding = 5
}

-- Função para criar uma nova janela
function MyLibrary:NewWindow(windowName)
    -- Verificar se a UI já existe e removê-la
    if game:GetService("CoreGui"):FindFirstChild("MyLibraryUI") then
        game:GetService("CoreGui"):FindFirstChild("MyLibraryUI"):Destroy()
    end
    
    -- Criar elementos da UI principal
    local MyLibraryUI = Instance.new("ScreenGui")
    local Main = Instance.new("Frame")
    local UICorner = Instance.new("UICorner")
    local TopBar = Instance.new("Frame")
    local TopBarUICorner = Instance.new("UICorner")
    local Title = Instance.new("TextLabel")
    local Close = Instance.new("TextButton")
    local TabHolder = Instance.new("Frame")
    local TabsContainer = Instance.new("Frame")
    local UIListLayout = Instance.new("UIListLayout")
    local ContentContainer = Instance.new("Frame")
    
    -- Configurar a UI principal
    MyLibraryUI.Name = "MyLibraryUI"
    MyLibraryUI.Parent = game:GetService("CoreGui")
    MyLibraryUI.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    
    Main.Name = "Main"
    Main.Parent = MyLibraryUI
    Main.BackgroundColor3 = UIConfig.WindowColor
    Main.Position = UDim2.new(0.5, -UIConfig.WindowWidth/2, 0.5, -UIConfig.WindowHeight/2)
    Main.Size = UDim2.new(0, UIConfig.WindowWidth, 0, UIConfig.WindowHeight)
    Main.Active = true
    Main.Draggable = true
    
    UICorner.Parent = Main
    UICorner.CornerRadius = UIConfig.CornerRadius
    
    TopBar.Name = "TopBar"
    TopBar.Parent = Main
    TopBar.BackgroundColor3 = UIConfig.AccentColor
    TopBar.Size = UDim2.new(1, 0, 0, 30)
    
    TopBarUICorner.Parent = TopBar
    TopBarUICorner.CornerRadius = UIConfig.CornerRadius
    
    Title.Name = "Title"
    Title.Parent = TopBar
    Title.BackgroundTransparency = 1
    Title.Position = UDim2.new(0, 10, 0, 0)
    Title.Size = UDim2.new(1, -40, 1, 0)
    Title.Font = UIConfig.Font
    Title.Text = windowName or "Minha UI Library"
    Title.TextColor3 = UIConfig.TextColor
    Title.TextSize = 18
    Title.TextXAlignment = Enum.TextXAlignment.Left
    
    Close.Name = "Close"
    Close.Parent = TopBar
    Close.BackgroundTransparency = 1
    Close.Position = UDim2.new(1, -25, 0, 5)
    Close.Size = UDim2.new(0, 20, 0, 20)
    Close.Font = UIConfig.Font
    Close.Text = "X"
    Close.TextColor3 = UIConfig.TextColor
    Close.TextSize = 18
    Close.MouseButton1Click:Connect(function()
        MyLibraryUI:Destroy()
    end)
    
    TabHolder.Name = "TabHolder"
    TabHolder.Parent = Main
    TabHolder.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
    TabHolder.Position = UDim2.new(0, 5, 0, 35)
    TabHolder.Size = UDim2.new(0, 120, 1, -40)
    
    local TabHolderUICorner = Instance.new("UICorner")
    TabHolderUICorner.Parent = TabHolder
    TabHolderUICorner.CornerRadius = UIConfig.CornerRadius
    
    TabsContainer.Name = "TabsContainer"
    TabsContainer.Parent = TabHolder
    TabsContainer.BackgroundTransparency = 1
    TabsContainer.Position = UDim2.new(0, 5, 0, 5)
    TabsContainer.Size = UDim2.new(1, -10, 1, -10)
    
    UIListLayout.Parent = TabsContainer
    UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
    UIListLayout.Padding = UDim.new(0, 5)
    
    ContentContainer.Name = "ContentContainer"
    ContentContainer.Parent = Main
    ContentContainer.BackgroundTransparency = 1
    ContentContainer.Position = UDim2.new(0, 130, 0, 35)
    ContentContainer.Size = UDim2.new(1, -135, 1, -40)
    
    -- Objeto da janela
    local Window = {}
    local Tabs = {}
    local ActiveTab = nil
    
    -- Função para criar uma nova seção (tab)
    function Window:NewSection(sectionName)
        -- Criar botão do tab
        local TabButton = Instance.new("TextButton")
        local TabContent = Instance.new("ScrollingFrame")
        local ElementsLayout = Instance.new("UIListLayout")
        
        TabButton.Name = "Tab_" .. sectionName
        TabButton.Parent = TabsContainer
        TabButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
        TabButton.Size = UDim2.new(1, 0, 0, 30)
        TabButton.Font = UIConfig.Font
        TabButton.Text = sectionName
        TabButton.TextColor3 = UIConfig.TextColor
        TabButton.TextSize = 14
        TabButton.AutoButtonColor = false
        
        local TabButtonUICorner = Instance.new("UICorner")
        TabButtonUICorner.Parent = TabButton
        TabButtonUICorner.CornerRadius = UIConfig.CornerRadius
        
        TabContent.Name = "Content_" .. sectionName
        TabContent.Parent = ContentContainer
        TabContent.BackgroundTransparency = 1
        TabContent.Size = UDim2.new(1, 0, 1, 0)
        TabContent.ScrollBarThickness = 4
        TabContent.Visible = false
        TabContent.CanvasSize = UDim2.new(0, 0, 0, 0)
        TabContent.ScrollingDirection = Enum.ScrollingDirection.Y
        TabContent.AutomaticCanvasSize = Enum.AutomaticSize.Y
        
        ElementsLayout.Parent = TabContent
        ElementsLayout.SortOrder = Enum.SortOrder.LayoutOrder
        ElementsLayout.Padding = UDim.new(0, UIConfig.ElementPadding)
        
        -- Adicionar o tab à lista de tabs
        table.insert(Tabs, {
            Button = TabButton,
            Content = TabContent,
            Name = sectionName
        })
        
        -- Se for o primeiro tab, ativar por padrão
        if #Tabs == 1 then
            TabContent.Visible = true
            TabButton.BackgroundColor3 = UIConfig.AccentColor
            ActiveTab = Tabs[1]
        end
        
        -- Função para alternar entre tabs
        TabButton.MouseButton1Click:Connect(function()
            if ActiveTab then
                ActiveTab.Content.Visible = false
                ActiveTab.Button.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
            end
            
            TabContent.Visible = true
            TabButton.BackgroundColor3 = UIConfig.AccentColor
            ActiveTab = {
                Button = TabButton,
                Content = TabContent,
                Name = sectionName
            }
        end)
        
        -- Objeto do tab com funções para criar elementos
        local Tab = {}
        local ElementCount = 0
        
        -- Função para criar um botão
        function Tab:CreateButton(buttonText, callback)
            ElementCount = ElementCount + 1
            
            local Button = Instance.new("Frame")
            local ButtonUI = Instance.new("TextButton")
            local ButtonUICorner = Instance.new("UICorner")
            
            Button.Name = "Button_" .. buttonText
            Button.Parent = TabContent
            Button.BackgroundTransparency = 1
            Button.Size = UDim2.new(1, 0, 0, UIConfig.ElementHeight)
            Button.LayoutOrder = ElementCount
            
            ButtonUI.Name = "ButtonUI"
            ButtonUI.Parent = Button
            ButtonUI.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
            ButtonUI.Size = UDim2.new(1, -10, 1, 0)
            ButtonUI.Position = UDim2.new(0, 5, 0, 0)
            ButtonUI.Font = UIConfig.Font
            ButtonUI.Text = buttonText
            ButtonUI.TextColor3 = UIConfig.TextColor
            ButtonUI.TextSize = 14
            ButtonUI.AutoButtonColor = false
            
            ButtonUICorner.Parent = ButtonUI
            ButtonUICorner.CornerRadius = UIConfig.CornerRadius
            
            -- Efeito de hover
            ButtonUI.MouseEnter:Connect(function()
                TweenService:Create(ButtonUI, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(60, 60, 60)}):Play()
            end)
            
            ButtonUI.MouseLeave:Connect(function()
                TweenService:Create(ButtonUI, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(40, 40, 40)}):Play()
            end)
            
            -- Efeito de clique
            ButtonUI.MouseButton1Down:Connect(function()
                TweenService:Create(ButtonUI, TweenInfo.new(0.1), {BackgroundColor3 = UIConfig.AccentColor}):Play()
            end)
            
            ButtonUI.MouseButton1Up:Connect(function()
                TweenService:Create(ButtonUI, TweenInfo.new(0.1), {BackgroundColor3 = Color3.fromRGB(60, 60, 60)}):Play()
            end)
            
            -- Callback do botão
            ButtonUI.MouseButton1Click:Connect(function()
                if callback then
                    callback()
                end
            end)
            
            return Button
        end
        
        -- Função para criar uma caixa de texto
        function Tab:CreateTextbox(textboxText, callback)
            ElementCount = ElementCount + 1
            
            local TextboxFrame = Instance.new("Frame")
            local TextboxLabel = Instance.new("TextLabel")
            local TextboxUI = Instance.new("TextBox")
            local TextboxUICorner = Instance.new("UICorner")
            
            TextboxFrame.Name = "Textbox_" .. textboxText
            TextboxFrame.Parent = TabContent
            TextboxFrame.BackgroundTransparency = 1
            TextboxFrame.Size = UDim2.new(1, 0, 0, UIConfig.ElementHeight)
            TextboxFrame.LayoutOrder = ElementCount
            
            TextboxLabel.Name = "TextboxLabel"
            TextboxLabel.Parent = TextboxFrame
            TextboxLabel.BackgroundTransparency = 1
            TextboxLabel.Position = UDim2.new(0, 5, 0, 0)
            TextboxLabel.Size = UDim2.new(0.5, -10, 1, 0)
            TextboxLabel.Font = UIConfig.Font
            TextboxLabel.Text = textboxText
            TextboxLabel.TextColor3 = UIConfig.TextColor
            TextboxLabel.TextSize = 14
            TextboxLabel.TextXAlignment = Enum.TextXAlignment.Left
            
            TextboxUI.Name = "TextboxUI"
            TextboxUI.Parent = TextboxFrame
            TextboxUI.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
            TextboxUI.Position = UDim2.new(0.5, 5, 0, 5)
            TextboxUI.Size = UDim2.new(0.5, -10, 1, -10)
            TextboxUI.Font = UIConfig.Font
            TextboxUI.PlaceholderText = "Digite aqui..."
            TextboxUI.Text = ""
            TextboxUI.TextColor3 = UIConfig.TextColor
            TextboxUI.TextSize = 14
            TextboxUI.ClearTextOnFocus = false
            
            TextboxUICorner.Parent = TextboxUI
            TextboxUICorner.CornerRadius = UIConfig.CornerRadius
            
            -- Callback da caixa de texto
            TextboxUI.FocusLost:Connect(function(enterPressed)
                if enterPressed and callback then
                    callback(TextboxUI.Text)
                end
            end)
            
            return TextboxFrame
        end
        
        -- Função para criar um dropdown
        function Tab:CreateDropdown(dropdownText, options, defaultOption, callback)
            ElementCount = ElementCount + 1
            
            local DropdownFrame = Instance.new("Frame")
            local DropdownLabel = Instance.new("TextLabel")
            local DropdownButton = Instance.new("TextButton")
            local DropdownUICorner = Instance.new("UICorner")
            local DropdownContainer = Instance.new("Frame")
            local DropdownContainerUICorner = Instance.new("UICorner")
            local DropdownOptionsLayout = Instance.new("UIListLayout")
            
            DropdownFrame.Name = "Dropdown_" .. dropdownText
            DropdownFrame.Parent = TabContent
            DropdownFrame.BackgroundTransparency = 1
            DropdownFrame.Size = UDim2.new(1, 0, 0, UIConfig.ElementHeight)
            DropdownFrame.LayoutOrder = ElementCount
            
            DropdownLabel.Name = "DropdownLabel"
            DropdownLabel.Parent = DropdownFrame
            DropdownLabel.BackgroundTransparency = 1
            DropdownLabel.Position = UDim2.new(0, 5, 0, 0)
            DropdownLabel.Size = UDim2.new(0.5, -10, 1, 0)
            DropdownLabel.Font = UIConfig.Font
            DropdownLabel.Text = dropdownText
            DropdownLabel.TextColor3 = UIConfig.TextColor
            DropdownLabel.TextSize = 14
            DropdownLabel.TextXAlignment = Enum.TextXAlignment.Left
            
            DropdownButton.Name = "DropdownButton"
            DropdownButton.Parent = DropdownFrame
            DropdownButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
            DropdownButton.Position = UDim2.new(0.5, 5, 0, 5)
            DropdownButton.Size = UDim2.new(0.5, -10, 1, -10)
            DropdownButton.Font = UIConfig.Font
            DropdownButton.Text = options[defaultOption] or "Selecione..."
            DropdownButton.TextColor3 = UIConfig.TextColor
            DropdownButton.TextSize = 14
            DropdownButton.AutoButtonColor = false
            
            DropdownUICorner.Parent = DropdownButton
            DropdownUICorner.CornerRadius = UIConfig.CornerRadius
            
            DropdownContainer.Name = "DropdownContainer"
            DropdownContainer.Parent = DropdownFrame
            DropdownContainer.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
            DropdownContainer.Position = UDim2.new(0.5, 5, 1, 0)
            DropdownContainer.Size = UDim2.new(0.5, -10, 0, 0)
            DropdownContainer.Visible = false
            DropdownContainer.ZIndex = 5
            DropdownContainer.ClipsDescendants = true
            
            DropdownContainerUICorner.Parent = DropdownContainer
            DropdownContainerUICorner.CornerRadius = UIConfig.CornerRadius
            
            DropdownOptionsLayout.Parent = DropdownContainer
            DropdownOptionsLayout.SortOrder = Enum.SortOrder.LayoutOrder
            
            -- Variáveis para controle do dropdown
            local IsOpen = false
            local SelectedOption = options[defaultOption] or "Selecione..."
            
            -- Adicionar opções ao dropdown
            for i, option in ipairs(options) do
                local OptionButton = Instance.new("TextButton")
                
                OptionButton.Name = "Option_" .. option
                OptionButton.Parent = DropdownContainer
                OptionButton.BackgroundTransparency = 1
                OptionButton.Size = UDim2.new(1, 0, 0, 25)
                OptionButton.Font = UIConfig.Font
                OptionButton.Text = option
                OptionButton.TextColor3 = UIConfig.TextColor
                OptionButton.TextSize = 14
                OptionButton.ZIndex = 6
                
                -- Efeito de hover na opção
                OptionButton.MouseEnter:Connect(function()
                    TweenService:Create(OptionButton, TweenInfo.new(0.1), {BackgroundTransparency = 0.8, BackgroundColor3 = UIConfig.AccentColor}):Play()
                end)
                
                OptionButton.MouseLeave:Connect(function()
                    TweenService:Create(OptionButton, TweenInfo.new(0.1), {BackgroundTransparency = 1}):Play()
                end)
                
                -- Seleção da opção
                OptionButton.MouseButton1Click:Connect(function()
                    SelectedOption = option
                    DropdownButton.Text = option
                    
                    -- Fechar o dropdown
                    IsOpen = false
                    TweenService:Create(DropdownContainer, TweenInfo.new(0.2), {Size = UDim2.new(0.5, -10, 0, 0)}):Play()
                    wait(0.2)
                    DropdownContainer.Visible = false
                    
                    if callback then
                        callback(option)
                    end
                end)
            end
            
            -- Toggle do dropdown
            DropdownButton.MouseButton1Click:Connect(function()
                if IsOpen then
                    -- Fechar dropdown
                    IsOpen = false
                    TweenService:Create(DropdownContainer, TweenInfo.new(0.2), {Size = UDim2.new(0.5, -10, 0, 0)}):Play()
                    wait(0.2)
                    DropdownContainer.Visible = false
                else
                    -- Abrir dropdown
                    IsOpen = true
                    DropdownContainer.Visible = true
                    TweenService:Create(DropdownContainer, TweenInfo.new(0.2), {Size = UDim2.new(0.5, -10, 0, #options * 25)}):Play()
                end
            end)
            
            -- Se um valor padrão foi fornecido, chamar o callback
            if defaultOption and options[defaultOption] and callback then
                callback(options[defaultOption])
            end
            
            return DropdownFrame
        end
        
        -- Função para criar um slider
        function Tab:CreateSlider(sliderText, min, max, default, precise, callback)
            ElementCount = ElementCount + 1
            
            local SliderFrame = Instance.new("Frame")
            local SliderLabel = Instance.new("TextLabel")
            local SliderValue = Instance.new("TextLabel")
            local SliderUI = Instance.new("Frame")
            local SliderUICorner = Instance.new("UICorner")
            local SliderFill = Instance.new("Frame")
            local SliderFillUICorner = Instance.new("UICorner")
            local SliderDrag = Instance.new("TextButton")
            local SliderDragUICorner = Instance.new("UICorner")
            
            SliderFrame.Name = "Slider_" .. sliderText
            SliderFrame.Parent = TabContent
            SliderFrame.BackgroundTransparency = 1
            SliderFrame.Size = UDim2.new(1, 0, 0, UIConfig.ElementHeight)
            SliderFrame.LayoutOrder = ElementCount
            
            SliderLabel.Name = "SliderLabel"
            SliderLabel.Parent = SliderFrame
            SliderLabel.BackgroundTransparency = 1
            SliderLabel.Position = UDim2.new(0, 5, 0, 0)
            SliderLabel.Size = UDim2.new(0.5, -10, 0.5, 0)
            SliderLabel.Font = UIConfig.Font
            SliderLabel.Text = sliderText
            SliderLabel.TextColor3 = UIConfig.TextColor
            SliderLabel.TextSize = 14
            SliderLabel.TextXAlignment = Enum.TextXAlignment.Left
            
            SliderValue.Name = "SliderValue"
            SliderValue.Parent = SliderFrame
            SliderValue.BackgroundTransparency = 1
            SliderValue.Position = UDim2.new(0.5, 5, 0, 0)
            SliderValue.Size = UDim2.new(0.5, -10, 0.5, 0)
            SliderValue.Font = UIConfig.Font
            SliderValue.Text = tostring(default)
            SliderValue.TextColor3 = UIConfig.TextColor
            SliderValue.TextSize = 14
            SliderValue.TextXAlignment = Enum.TextXAlignment.Right
            
            SliderUI.Name = "SliderUI"
            SliderUI.Parent = SliderFrame
            SliderUI.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
            SliderUI.Position = UDim2.new(0, 5, 0.5, 2)
            SliderUI.Size = UDim2.new(1, -10, 0, 10)
            
            SliderUICorner.Parent = SliderUI
            SliderUICorner.CornerRadius = UDim.new(0, 5)
            
            SliderFill.Name = "SliderFill"
            SliderFill.Parent = SliderUI
            SliderFill.BackgroundColor3 = UIConfig.AccentColor
            SliderFill.Size = UDim2.new(0, 0, 1, 0)
            
            SliderFillUICorner.Parent = SliderFill
            SliderFillUICorner.CornerRadius = UDim.new(0, 5)
            
            SliderDrag.Name = "SliderDrag"
            SliderDrag.Parent = SliderFill
            SliderDrag.AnchorPoint = Vector2.new(1, 0.5)
            SliderDrag.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
            SliderDrag.Position = UDim2.new(1, 0, 0.5, 0)
            SliderDrag.Size = UDim2.new(0, 12, 0, 12)
            SliderDrag.Text = ""
            SliderDrag.AutoButtonColor = false
            
            SliderDragUICorner.Parent = SliderDrag
            SliderDragUICorner.CornerRadius = UDim.new(1, 0)
            
            -- Configurar valor padrão
            local function SetValue(value)
                value = math.clamp(value, min, max)
                
                if not precise then
                    value = math.floor(value)
                end
                
                local percent = (value - min) / (max - min)
                SliderFill:TweenSize(UDim2.new(percent, 0, 1, 0), "Out", "Quad", 0.1, true)
                SliderValue.Text = tostring(value)
                
                if callback then
                    callback(value)
                end
            end
            
            -- Configurar interação do slider
            local dragging = false
            
            SliderDrag.MouseButton1Down:Connect(function()
                dragging = true
            end)
            
            UserInputService.InputEnded:Connect(function(input)
                if input.UserInputType == Enum.UserInputType.MouseButton1 then
                    dragging = false
                end
            end)
            
            UserInputService.InputChanged:Connect(function(input)
                if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
                    local mousePos = UserInputService:GetMouseLocation()
                    local sliderPos = SliderUI.AbsolutePosition
                    local sliderSize = SliderUI.AbsoluteSize
                    
                    local relativeX = math.clamp((mousePos.X - sliderPos.X) / sliderSize.X, 0, 1)
                    local value = min + (max - min) * relativeX
                    
                    SetValue(value)
                end
            end)
            
            -- Definir valor inicial
            SetValue(default)
            
            return SliderFrame
        end
        
        -- Função para criar um color picker
        function Tab:CreateColorPicker(pickerText, defaultColor, callback)
            ElementCount = ElementCount + 1
            
            local ColorPickerFrame = Instance.new("Frame")
            local ColorPickerLabel = Instance.new("TextLabel")
            local ColorDisplay = Instance.new("TextButton")
            local ColorDisplayUICorner = Instance.new("UICorner")
            
            ColorPickerFrame.Name = "ColorPicker_" .. pickerText
            ColorPickerFrame.Parent = TabContent
            ColorPickerFrame.BackgroundTransparency = 1
            ColorPickerFrame.Size = UDim2.new(1, 0, 0, UIConfig.ElementHeight)
            ColorPickerFrame.LayoutOrder = ElementCount
            
            ColorPickerLabel.Name = "ColorPickerLabel"
            ColorPickerLabel.Parent = ColorPickerFrame
            ColorPickerLabel.BackgroundTransparency = 1
            ColorPickerLabel.Position = UDim2.new(0, 5, 0, 0)
            ColorPickerLabel.Size = UDim2.new(0.75, -10, 1, 0)
            ColorPickerLabel.Font = UIConfig.Font
            ColorPickerLabel.Text = pickerText
            ColorPickerLabel.TextColor3 = UIConfig.TextColor
            ColorPickerLabel.TextSize = 14
            ColorPickerLabel.TextXAlignment = Enum.TextXAlignment.Left
            
            ColorDisplay.Name = "ColorDisplay"
            ColorDisplay.Parent = ColorPickerFrame
            ColorDisplay.BackgroundColor3 = defaultColor
            ColorDisplay.Position = UDim2.new(0.75, 5, 0.5, -10)
            ColorDisplay.Size = UDim2.new(0.25, -10, 0, 20)
            ColorDisplay.Text = ""
            ColorDisplay.AutoButtonColor = false
            
            ColorDisplayUICorner.Parent = ColorDisplay
            ColorDisplayUICorner.CornerRadius = UIConfig.CornerRadius
            
            -- Para uma implementação completa do color picker, seria necessário um popup
            -- Aqui estamos simplificando com apenas algumas cores predefinidas para exemplo
            local isPickerOpen = false
            local pickerContainer = Instance.new("Frame")
            local pickerContainerLayout = Instance.new("UIGridLayout")
            
            pickerContainer.Name = "PickerContainer"
            pickerContainer.Parent = ColorPickerFrame
            pickerContainer.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
            pickerContainer.Position = UDim2.new(0.75, 5, 1, 5)
            pickerContainer.Size = UDim2.new(0.25, -10, 0, 80)
            pickerContainer.Visible = false
            pickerContainer.ZIndex = 10
            
            local pickerContainerUICorner = Instance.new("UICorner")
            pickerContainerUICorner.Parent = pickerContainer
            pickerContainerUICorner.CornerRadius = UIConfig.CornerRadius
            
            pickerContainerLayout.Parent = pickerContainer
            pickerContainerLayout.SortOrder = Enum.SortOrder.LayoutOrder
            pickerContainerLayout.CellSize = UDim2.new(0, 20, 0, 20)
            pickerContainerLayout.CellPadding = UDim2.new(0, 5, 0, 5)
            pickerContainerLayout.FillDirectionMaxCells = 3
            pickerContainerLayout.StartCorner = Enum.StartCorner.TopLeft
            
            -- Algumas cores predefinidas
            local presetColors = {
                Color3.fromRGB(255, 0, 0),      -- Vermelho
                Color3.fromRGB(0, 255, 0),      -- Verde
                Color3.fromRGB(0, 0, 255),      -- Azul
                Color3.fromRGB(255, 255, 0),    -- Amarelo
                Color3.fromRGB(255, 0, 255),    -- Magenta
                Color3.fromRGB(0, 255, 255),    -- Ciano
                Color3.fromRGB(255, 255, 255),  -- Branco
                Color3.fromRGB(0, 0, 0),        -- Preto
                Color3.fromRGB(128, 128, 128)   -- Cinza
            }
            
            -- Criar botões de cores
            for i, color in ipairs(presetColors) do
                local colorButton = Instance.new("TextButton")
                colorButton.Name = "ColorOption_" .. i
                colorButton.Parent = pickerContainer
                colorButton.BackgroundColor3 = color
                colorButton.Size = UDim2.new(1, 0, 1, 0)
                colorButton.Text = ""
                colorButton.ZIndex = 11
                
                local colorButtonUICorner = Instance.new("UICorner")
                colorButtonUICorner.Parent = colorButton
                colorButtonUICorner.CornerRadius = UDim.new(0, 4)
                
                -- Ao clicar na cor
                colorButton.MouseButton1Click:Connect(function()
                    ColorDisplay.BackgroundColor3 = color
                    
                    -- Fechar o seletor
                    isPickerOpen = false
                    pickerContainer.Visible = false
                    
                    if callback then
                        callback(color)
                    end
                end)
            end
            
            -- Toggle do picker
            ColorDisplay.MouseButton1Click:Connect(function()
                isPickerOpen = not isPickerOpen
                pickerContainer.Visible = isPickerOpen
            end)
            
            -- Chamada inicial do callback com a cor padrão
            if callback then
                callback(defaultColor)
            end
            
            return ColorPickerFrame
        end
        
        -- Função para criar um toggle (checkbox)
        function Tab:CreateToggle(toggleText, defaultState, callback)
            ElementCount = ElementCount + 1
            
            local ToggleFrame = Instance.new("Frame")
            local ToggleLabel = Instance.new("TextLabel")
            local ToggleButton = Instance.new("TextButton")
            local ToggleUICorner = Instance.new("UICorner")
            local ToggleIndicator = Instance.new("Frame")
            local ToggleIndicatorUICorner = Instance.new("UICorner")
            
            ToggleFrame.Name = "Toggle_" .. toggleText
            ToggleFrame.Parent = TabContent
            ToggleFrame.BackgroundTransparency = 1
            ToggleFrame.Size = UDim2.new(1, 0, 0, UIConfig.ElementHeight)
            ToggleFrame.LayoutOrder = ElementCount
            
            ToggleLabel.Name = "ToggleLabel"
            ToggleLabel.Parent = ToggleFrame
            ToggleLabel.BackgroundTransparency = 1
            ToggleLabel.Position = UDim2.new(0, 5, 0, 0)
            ToggleLabel.Size = UDim2.new(0.75, -10, 1, 0)
            ToggleLabel.Font = UIConfig.Font
            ToggleLabel.Text = toggleText
            ToggleLabel.TextColor3 = UIConfig.TextColor
            ToggleLabel.TextSize = 14
            ToggleLabel.TextXAlignment = Enum.TextXAlignment.Left
            
            ToggleButton.Name = "ToggleButton"
            ToggleButton.Parent = ToggleFrame
            ToggleButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
            ToggleButton.Position = UDim2.new(0.75, 5, 0.5, -10)
            ToggleButton.Size = UDim2.new(0.25, -10, 0, 20)
            ToggleButton.Text = ""
            ToggleButton.AutoButtonColor = false
            
            ToggleUICorner.Parent = ToggleButton
            ToggleUICorner.CornerRadius = UIConfig.CornerRadius
            
            ToggleIndicator.Name = "ToggleIndicator"
            ToggleIndicator.Parent = ToggleButton
            ToggleIndicator.AnchorPoint = Vector2.new(0, 0.5)
            ToggleIndicator.BackgroundColor3 = UIConfig.TextColor
            ToggleIndicator.Position = UDim2.new(0, 2, 0.5, 0)
            ToggleIndicator.Size = UDim2.new(0, 16, 0, 16)
            
            ToggleIndicatorUICorner.Parent = ToggleIndicator
            ToggleIndicatorUICorner.CornerRadius = UDim.new(0, 4)
            
            -- Estado do toggle
            local Enabled = defaultState or false
            
            -- Função para atualizar o estado visual
            local function UpdateToggle()
                if Enabled then
                    TweenService:Create(ToggleIndicator, TweenInfo.new(0.2), {
                        Position = UDim2.new(1, -18, 0.5, 0),
                        BackgroundColor3 = UIConfig.AccentColor
                    }):Play()
                    TweenService:Create(ToggleButton, TweenInfo.new(0.2), {
                        BackgroundColor3 = Color3.fromRGB(60, 60, 60)
                    }):Play()
                else
                    TweenService:Create(ToggleIndicator, TweenInfo.new(0.2), {
                        Position = UDim2.new(0, 2, 0.5, 0),
                        BackgroundColor3 = UIConfig.TextColor
                    }):Play()
                    TweenService:Create(ToggleButton, TweenInfo.new(0.2), {
                        BackgroundColor3 = Color3.fromRGB(40, 40, 40)
                    }):Play()
                end
            end
            
            -- Configurar estado inicial
            UpdateToggle()
            
            -- Evento de clique
            ToggleButton.MouseButton1Click:Connect(function()
                Enabled = not Enabled
                UpdateToggle()
                
                if callback then
                    callback(Enabled)
                end
            end)
            
            -- Chamada inicial do callback com o estado padrão
            if callback and defaultState then
                callback(defaultState)
            end
            
            return ToggleFrame
        end
        
        -- Função para criar uma label simples
        function Tab:CreateLabel(labelText)
            ElementCount = ElementCount + 1
            
            local LabelFrame = Instance.new("Frame")
            local Label = Instance.new("TextLabel")
            
            LabelFrame.Name = "Label_" .. labelText
            LabelFrame.Parent = TabContent
            LabelFrame.BackgroundTransparency = 1
            LabelFrame.Size = UDim2.new(1, 0, 0, UIConfig.ElementHeight/1.5)
            LabelFrame.LayoutOrder = ElementCount
            
            Label.Name = "Label"
            Label.Parent = LabelFrame
            Label.BackgroundTransparency = 1
            Label.Position = UDim2.new(0, 5, 0, 0)
            Label.Size = UDim2.new(1, -10, 1, 0)
            Label.Font = UIConfig.Font
            Label.Text = labelText
            Label.TextColor3 = UIConfig.TextColor
            Label.TextSize = 14
            Label.TextXAlignment = Enum.TextXAlignment.Left
            Label.TextWrapped = true
            
            -- Função para atualizar a label
            local LabelObj = {}
            
            function LabelObj:UpdateText(newText)
                Label.Text = newText
            end
            
            return LabelObj
        end
        
        -- Função para criar um separador
        function Tab:CreateSeparator()
            ElementCount = ElementCount + 1
            
            local SeparatorFrame = Instance.new("Frame")
            local Separator = Instance.new("Frame")
            
            SeparatorFrame.Name = "Separator_" .. ElementCount
            SeparatorFrame.Parent = TabContent
            SeparatorFrame.BackgroundTransparency = 1
            SeparatorFrame.Size = UDim2.new(1, 0, 0, 10)
            SeparatorFrame.LayoutOrder = ElementCount
            
            Separator.Name = "Line"
            Separator.Parent = SeparatorFrame
            Separator.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
            Separator.BorderSizePixel = 0
            Separator.Position = UDim2.new(0, 5, 0.5, 0)
            Separator.Size = UDim2.new(1, -10, 0, 1)
            
            return SeparatorFrame
        end
        
        -- Função para criar uma lista de itens
        function Tab:CreateList(listText, items, callback)
            ElementCount = ElementCount + 1
            
            local ListFrame = Instance.new("Frame")
            local ListLabel = Instance.new("TextLabel")
            local ListContainer = Instance.new("ScrollingFrame")
            local ListUICorner = Instance.new("UICorner")
            local ListLayout = Instance.new("UIListLayout")
            
            ListFrame.Name = "List_" .. listText
            ListFrame.Parent = TabContent
            ListFrame.BackgroundTransparency = 1
            ListFrame.Size = UDim2.new(1, 0, 0, UIConfig.ElementHeight * 3)
            ListFrame.LayoutOrder = ElementCount
            
            ListLabel.Name = "ListLabel"
            ListLabel.Parent = ListFrame
            ListLabel.BackgroundTransparency = 1
            ListLabel.Position = UDim2.new(0, 5, 0, 0)
            ListLabel.Size = UDim2.new(1, -10, 0, 20)
            ListLabel.Font = UIConfig.Font
            ListLabel.Text = listText
            ListLabel.TextColor3 = UIConfig.TextColor
            ListLabel.TextSize = 14
            ListLabel.TextXAlignment = Enum.TextXAlignment.Left
            
            ListContainer.Name = "ListContainer"
            ListContainer.Parent = ListFrame
            ListContainer.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
            ListContainer.Position = UDim2.new(0, 5, 0, 25)
            ListContainer.Size = UDim2.new(1, -10, 0, 75)
            ListContainer.ScrollBarThickness = 4
            ListContainer.CanvasSize = UDim2.new(0, 0, 0, 0)
            ListContainer.AutomaticCanvasSize = Enum.AutomaticSize.Y
            ListContainer.ScrollingDirection = Enum.ScrollingDirection.Y
            
            ListUICorner.Parent = ListContainer
            ListUICorner.CornerRadius = UIConfig.CornerRadius
            
            ListLayout.Parent = ListContainer
            ListLayout.SortOrder = Enum.SortOrder.LayoutOrder
            ListLayout.Padding = UDim.new(0, 2)
            
            -- Adicionar itens à lista
            local selectedItem = nil
            
            for i, item in ipairs(items) do
                local ItemButton = Instance.new("TextButton")
                local ItemUICorner = Instance.new("UICorner")
                
                ItemButton.Name = "Item_" .. i
                ItemButton.Parent = ListContainer
                ItemButton.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
                ItemButton.Size = UDim2.new(1, -8, 0, 25)
                ItemButton.Position = UDim2.new(0, 4, 0, 0)
                ItemButton.Font = UIConfig.Font
                ItemButton.Text = item
                ItemButton.TextColor3 = UIConfig.TextColor
                ItemButton.TextSize = 14
                ItemButton.AutoButtonColor = false
                
                ItemUICorner.Parent = ItemButton
                ItemUICorner.CornerRadius = UIConfig.CornerRadius
                
                -- Efeito de hover
                ItemButton.MouseEnter:Connect(function()
                    if selectedItem ~= item then
                        TweenService:Create(ItemButton, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(55, 55, 55)}):Play()
                    end
                end)
                
                ItemButton.MouseLeave:Connect(function()
                    if selectedItem ~= item then
                        TweenService:Create(ItemButton, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(45, 45, 45)}):Play()
                    end
                end)
                
                -- Seleção do item
                ItemButton.MouseButton1Click:Connect(function()
                    -- Restaurar item anteriormente selecionado
                    if selectedItem then
                        for _, child in pairs(ListContainer:GetChildren()) do
                            if child:IsA("TextButton") and child.Text == selectedItem then
                                TweenService:Create(child, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(45, 45, 45)}):Play()
                                break
                            end
                        end
                    end
                    
                    -- Selecionar novo item
                    selectedItem = item
                    TweenService:Create(ItemButton, TweenInfo.new(0.2), {BackgroundColor3 = UIConfig.AccentColor}):Play()
                    
                    if callback then
                        callback(item)
                    end
                end)
            end
            
            return ListFrame
        end
        
        -- Função para criar um campo numérico
        function Tab:CreateNumeric(numericText, min, max, default, callback)
            ElementCount = ElementCount + 1
            
            local NumericFrame = Instance.new("Frame")
            local NumericLabel = Instance.new("TextLabel")
            local NumericValue = Instance.new("TextBox")
            local NumericUICorner = Instance.new("UICorner")
            local NumericDecrease = Instance.new("TextButton")
            local NumericIncrease = Instance.new("TextButton")
            
            NumericFrame.Name = "Numeric_" .. numericText
            NumericFrame.Parent = TabContent
            NumericFrame.BackgroundTransparency = 1
            NumericFrame.Size = UDim2.new(1, 0, 0, UIConfig.ElementHeight)
            NumericFrame.LayoutOrder = ElementCount
            
            NumericLabel.Name = "NumericLabel"
            NumericLabel.Parent = NumericFrame
            NumericLabel.BackgroundTransparency = 1
            NumericLabel.Position = UDim2.new(0, 5, 0, 0)
            NumericLabel.Size = UDim2.new(0.5, -10, 1, 0)
            NumericLabel.Font = UIConfig.Font
            NumericLabel.Text = numericText
            NumericLabel.TextColor3 = UIConfig.TextColor
            NumericLabel.TextSize = 14
            NumericLabel.TextXAlignment = Enum.TextXAlignment.Left
            
            NumericValue.Name = "NumericValue"
            NumericValue.Parent = NumericFrame
            NumericValue.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
            NumericValue.Position = UDim2.new(0.5, 5, 0.5, -12)
            NumericValue.Size = UDim2.new(0.3, -10, 0, 24)
            NumericValue.Font = UIConfig.Font
            NumericValue.Text = tostring(default)
            NumericValue.TextColor3 = UIConfig.TextColor
            NumericValue.TextSize = 14
            NumericValue.ClearTextOnFocus = true
            
            NumericUICorner.Parent = NumericValue
            NumericUICorner.CornerRadius = UIConfig.CornerRadius
            
            NumericDecrease.Name = "NumericDecrease"
            NumericDecrease.Parent = NumericFrame
            NumericDecrease.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
            NumericDecrease.Position = UDim2.new(0.8, 5, 0.5, -12)
            NumericDecrease.Size = UDim2.new(0.1, -5, 0, 24)
            NumericDecrease.Font = UIConfig.Font
            NumericDecrease.Text = "-"
            NumericDecrease.TextColor3 = UIConfig.TextColor
            NumericDecrease.TextSize = 16
            NumericDecrease.AutoButtonColor = false
            
            local NumericDecreaseUICorner = Instance.new("UICorner")
            NumericDecreaseUICorner.Parent = NumericDecrease
            NumericDecreaseUICorner.CornerRadius = UIConfig.CornerRadius
            
            NumericIncrease.Name = "NumericIncrease"
            NumericIncrease.Parent = NumericFrame
            NumericIncrease.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
            NumericIncrease.Position = UDim2.new(0.9, 0, 0.5, -12)
            NumericIncrease.Size = UDim2.new(0.1, -5, 0, 24)
            NumericIncrease.Font = UIConfig.Font
            NumericIncrease.Text = "+"
            NumericIncrease.TextColor3 = UIConfig.TextColor
            NumericIncrease.TextSize = 16
            NumericIncrease.AutoButtonColor = false
            
            local NumericIncreaseUICorner = Instance.new("UICorner")
            NumericIncreaseUICorner.Parent = NumericIncrease
            NumericIncreaseUICorner.CornerRadius = UIConfig.CornerRadius
            
            -- Configurar valor
            local currentValue = default
            
            local function UpdateValue(newValue)
                -- Validar valor
                newValue = math.clamp(tonumber(newValue) or default, min, max)
                currentValue = newValue
                NumericValue.Text = tostring(newValue)
                
                if callback then
                    callback(newValue)
                end
            end
            
            -- Eventos dos botões e campo
            NumericDecrease.MouseButton1Click:Connect(function()
                UpdateValue(currentValue - 1)
            end)
            
            NumericIncrease.MouseButton1Click:Connect(function()
                UpdateValue(currentValue + 1)
            end)
            
            NumericValue.FocusLost:Connect(function(enterPressed)
                UpdateValue(NumericValue.Text)
            end)
            
            -- Chamada inicial do callback
            if callback then
                callback(default)
            end
            
            return NumericFrame
        end
        
        -- Função para criar um botão com imagem/ícone
        function Tab:CreateImageButton(buttonText, imageId, callback)
            ElementCount = ElementCount + 1
            
            local ImageButtonFrame = Instance.new("Frame")
            local ButtonUI = Instance.new("TextButton")
            local ButtonUICorner = Instance.new("UICorner")
            local ButtonImage = Instance.new("ImageLabel")
            local ButtonLabel = Instance.new("TextLabel")
            
            ImageButtonFrame.Name = "ImageButton_" .. buttonText
            ImageButtonFrame.Parent = TabContent
            ImageButtonFrame.BackgroundTransparency = 1
            ImageButtonFrame.Size = UDim2.new(1, 0, 0, UIConfig.ElementHeight)
            ImageButtonFrame.LayoutOrder = ElementCount
            
            ButtonUI.Name = "ButtonUI"
            ButtonUI.Parent = ImageButtonFrame
            ButtonUI.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
            ButtonUI.Size = UDim2.new(1, -10, 1, 0)
            ButtonUI.Position = UDim2.new(0, 5, 0, 0)
            ButtonUI.Font = UIConfig.Font
            ButtonUI.Text = ""
            ButtonUI.TextColor3 = UIConfig.TextColor
            ButtonUI.TextSize = 14
            ButtonUI.AutoButtonColor = false
            
            ButtonUICorner.Parent = ButtonUI
            ButtonUICorner.CornerRadius = UIConfig.CornerRadius
            
            ButtonImage.Name = "ButtonImage"
            ButtonImage.Parent = ButtonUI
            ButtonImage.BackgroundTransparency = 1
            ButtonImage.Size = UDim2.new(0, 20, 0, 20)
            ButtonImage.Position = UDim2.new(0, 5, 0.5, -10)
            ButtonImage.Image = "rbxassetid://" .. imageId
            
            ButtonLabel.Name = "ButtonLabel"
            ButtonLabel.Parent = ButtonUI
            ButtonLabel.BackgroundTransparency = 1
            ButtonLabel.Size = UDim2.new(1, -35, 1, 0)
            ButtonLabel.Position = UDim2.new(0, 30, 0, 0)
            ButtonLabel.Font = UIConfig.Font
            ButtonLabel.Text = buttonText
            ButtonLabel.TextColor3 = UIConfig.TextColor
            ButtonLabel.TextSize = 14
            ButtonLabel.TextXAlignment = Enum.TextXAlignment.Left
            
            -- Efeito de hover
            ButtonUI.MouseEnter:Connect(function()
                TweenService:Create(ButtonUI, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(60, 60, 60)}):Play()
            end)
            
            ButtonUI.MouseLeave:Connect(function()
                TweenService:Create(ButtonUI, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(40, 40, 40)}):Play()
            end)
            
            -- Efeito de clique
            ButtonUI.MouseButton1Down:Connect(function()
                TweenService:Create(ButtonUI, TweenInfo.new(0.1), {BackgroundColor3 = UIConfig.AccentColor}):Play()
            end)
            
            ButtonUI.MouseButton1Up:Connect(function()
                TweenService:Create(ButtonUI, TweenInfo.new(0.1), {BackgroundColor3 = Color3.fromRGB(60, 60, 60)}):Play()
            end)
            
            -- Callback do botão
            ButtonUI.MouseButton1Click:Connect(function()
                if callback then
                    callback()
                end
            end)
            
            return ImageButtonFrame
        end
        
        -- Função para criar uma caixa de código (para scripts)
        function Tab:CreateCodeBox(codeBoxText, defaultCode, callback)
            ElementCount = ElementCount + 1
            
            local CodeBoxFrame = Instance.new("Frame")
            local CodeBoxLabel = Instance.new("TextLabel")
            local CodeBoxContainer = Instance.new("Frame")
            local CodeBoxUICorner = Instance.new("UICorner")
            local CodeBoxScroll = Instance.new("ScrollingFrame")
            local CodeBoxText = Instance.new("TextBox")
            local RunButton = Instance.new("TextButton")
            local RunButtonUICorner = Instance.new("UICorner")
            
            CodeBoxFrame.Name = "CodeBox_" .. codeBoxText
            CodeBoxFrame.Parent = TabContent
            CodeBoxFrame.BackgroundTransparency = 1
            CodeBoxFrame.Size = UDim2.new(1, 0, 0, 150)
            CodeBoxFrame.LayoutOrder = ElementCount
            
            CodeBoxLabel.Name = "CodeBoxLabel"
            CodeBoxLabel.Parent = CodeBoxFrame
            CodeBoxLabel.BackgroundTransparency = 1
            CodeBoxLabel.Position = UDim2.new(0, 5, 0, 0)
            CodeBoxLabel.Size = UDim2.new(1, -10, 0, 20)
            CodeBoxLabel.Font = UIConfig.Font
            CodeBoxLabel.Text = codeBoxText
            CodeBoxLabel.TextColor3 = UIConfig.TextColor
            CodeBoxLabel.TextSize = 14
            CodeBoxLabel.TextXAlignment = Enum.TextXAlignment.Left
            
            CodeBoxContainer.Name = "CodeBoxContainer"
            CodeBoxContainer.Parent = CodeBoxFrame
            CodeBoxContainer.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
            CodeBoxContainer.Position = UDim2.new(0, 5, 0, 25)
            CodeBoxContainer.Size = UDim2.new(1, -10, 0, 90)
            
            CodeBoxUICorner.Parent = CodeBoxContainer
            CodeBoxUICorner.CornerRadius = UIConfig.CornerRadius
            
            CodeBoxScroll.Name = "CodeBoxScroll"
            CodeBoxScroll.Parent = CodeBoxContainer
            CodeBoxScroll.BackgroundTransparency = 1
            CodeBoxScroll.Position = UDim2.new(0, 5, 0, 5)
            CodeBoxScroll.Size = UDim2.new(1, -10, 1, -10)
            CodeBoxScroll.ScrollBarThickness = 4
            CodeBoxScroll.CanvasSize = UDim2.new(0, 0, 0, 0)
            CodeBoxScroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
            CodeBoxScroll.ScrollingDirection = Enum.ScrollingDirection.Y
            
            CodeBoxText.Name = "CodeBoxText"
            CodeBoxText.Parent = CodeBoxScroll
            CodeBoxText.BackgroundTransparency = 1
            CodeBoxText.Size = UDim2.new(1, 0, 0, 0)
            CodeBoxText.ClearTextOnFocus = false
            CodeBoxText.Font = Enum.Font.Code
            CodeBoxText.Text = defaultCode or ""
            CodeBoxText.TextColor3 = Color3.fromRGB(200, 200, 200)
            CodeBoxText.TextSize = 14
            CodeBoxText.TextXAlignment = Enum.TextXAlignment.Left
            CodeBoxText.TextYAlignment = Enum.TextYAlignment.Top
            CodeBoxText.AutomaticSize = Enum.AutomaticSize.Y
            CodeBoxText.MultiLine = true
            
            RunButton.Name = "RunButton"
            RunButton.Parent = CodeBoxFrame
            RunButton.BackgroundColor3 = UIConfig.AccentColor
            RunButton.Position = UDim2.new(0, 5, 0, 120)
            RunButton.Size = UDim2.new(0, 80, 0, 25)
            RunButton.Font = UIConfig.Font
            RunButton.Text = "Executar"
            RunButton.TextColor3 = UIConfig.TextColor
            RunButton.TextSize = 14
            RunButton.AutoButtonColor = false
            
            RunButtonUICorner.Parent = RunButton
            RunButtonUICorner.CornerRadius = UIConfig.CornerRadius
            
            -- Efeito de hover
            RunButton.MouseEnter:Connect(function()
                TweenService:Create(RunButton, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(0, 150, 220)}):Play()
            end)
            
            RunButton.MouseLeave:Connect(function()
                TweenService:Create(RunButton, TweenInfo.new(0.2), {BackgroundColor3 = UIConfig.AccentColor}):Play()
            end)
            
            -- Callback do botão
            RunButton.MouseButton1Click:Connect(function()
                if callback then
                    callback(CodeBoxText.Text)
                end
            end)
            
            return CodeBoxFrame
        end
        
        -- Função para criar um divisor com título
        function Tab:CreateDivider(dividerText)
            ElementCount = ElementCount + 1
            
            local DividerFrame = Instance.new("Frame")
            local DividerLine = Instance.new("Frame")
            local DividerLabel = Instance.new("TextLabel")
            
            DividerFrame.Name = "Divider_" .. dividerText
            DividerFrame.Parent = TabContent
            DividerFrame.BackgroundTransparency = 1
            DividerFrame.Size = UDim2.new(1, 0, 0, 20)
            DividerFrame.LayoutOrder = ElementCount
            
            DividerLine.Name = "DividerLine"
            DividerLine.Parent = DividerFrame
            DividerLine.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
            DividerLine.BorderSizePixel = 0
            DividerLine.Position = UDim2.new(0, 5, 0.5, 0)
            DividerLine.Size = UDim2.new(1, -10, 0, 1)
            
            DividerLabel.Name = "DividerLabel"
            DividerLabel.Parent = DividerFrame
            DividerLabel.BackgroundColor3 = UIConfig.WindowColor
            DividerLabel.BackgroundTransparency = 0
            DividerLabel.BorderSizePixel = 0
            DividerLabel.Position = UDim2.new(0.5, -50, 0, 0)
            DividerLabel.Size = UDim2.new(0, 100, 1, 0)
            DividerLabel.Font = UIConfig.Font
            DividerLabel.Text = dividerText
            DividerLabel.TextColor3 = UIConfig.TextColor
            DividerLabel.TextSize = 14
            
            return DividerFrame
        end
        
        -- Função para criar um grupo (elementos agrupados)
        function Tab:CreateGroup(groupText)
            ElementCount = ElementCount + 1
            
            local GroupFrame = Instance.new("Frame")
            local GroupBackground = Instance.new("Frame")
            local GroupUICorner = Instance.new("UICorner")
            local GroupLabel = Instance.new("TextLabel")
            local GroupContainer = Instance.new("Frame")
            local GroupLayout = Instance.new("UIListLayout")
            
            GroupFrame.Name = "Group_" .. groupText
            GroupFrame.Parent = TabContent
            GroupFrame.BackgroundTransparency = 1
            GroupFrame.Size = UDim2.new(1, 0, 0, 100)
            GroupFrame.LayoutOrder = ElementCount
            GroupFrame.AutomaticSize = Enum.AutomaticSize.Y
            
            GroupBackground.Name = "GroupBackground"
            GroupBackground.Parent = GroupFrame
            GroupBackground.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
            GroupBackground.Position = UDim2.new(0, 5, 0, 10)
            GroupBackground.Size = UDim2.new(1, -10, 1, -10)
            GroupBackground.AutomaticSize = Enum.AutomaticSize.Y
            
            GroupUICorner.Parent = GroupBackground
            GroupUICorner.CornerRadius = UIConfig.CornerRadius
            
            GroupLabel.Name = "GroupLabel"
            GroupLabel.Parent = GroupFrame
            GroupLabel.BackgroundColor3 = UIConfig.WindowColor
            GroupLabel.BackgroundTransparency = 0
            GroupLabel.BorderSizePixel = 0
            GroupLabel.Position = UDim2.new(0, 15, 0, 0)
            GroupLabel.Size = UDim2.new(0, 100, 0, 20)
            GroupLabel.Font = UIConfig.Font
            GroupLabel.Text = " " .. groupText .. " "
            GroupLabel.TextColor3 = UIConfig.TextColor
            GroupLabel.TextSize = 14
            
            GroupContainer.Name = "GroupContainer"
            GroupContainer.Parent = GroupBackground
            GroupContainer.BackgroundTransparency = 1
            GroupContainer.Position = UDim2.new(0, 5, 0, 10)
            GroupContainer.Size = UDim2.new(1, -10, 1, -15)
            GroupContainer.AutomaticSize = Enum.AutomaticSize.Y
            
            GroupLayout.Parent = GroupContainer
            GroupLayout.SortOrder = Enum.SortOrder.LayoutOrder
            GroupLayout.Padding = UDim.new(0, 5)
            
            -- Criar elementos dentro do grupo
            local Group = {}
            local GroupElementCount = 0
            
            -- Função para criar botão dentro do grupo
            function Group:AddButton(buttonText, callback)
                GroupElementCount = GroupElementCount + 1
                
                local ButtonFrame = Instance.new("Frame")
                local Button = Instance.new("TextButton")
                local ButtonUICorner = Instance.new("UICorner")
                
                ButtonFrame.Name = "Button_" .. buttonText
                ButtonFrame.Parent = GroupContainer
                ButtonFrame.BackgroundTransparency = 1
                ButtonFrame.Size = UDim2.new(1, 0, 0, 30)
                ButtonFrame.LayoutOrder = GroupElementCount
                
                Button.Name = "Button"
                Button.Parent = ButtonFrame
                Button.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
                Button.Size = UDim2.new(1, 0, 1, 0)
                Button.Font = UIConfig.Font
                Button.Text = buttonText
                Button.TextColor3 = UIConfig.TextColor
                Button.TextSize = 14
                Button.AutoButtonColor = false
                
                ButtonUICorner.Parent = Button
                ButtonUICorner.CornerRadius = UIConfig.CornerRadius
                
                -- Efeito de hover
                Button.MouseEnter:Connect(function()
                    TweenService:Create(Button, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(55, 55, 55)}):Play()
                end)
                
                Button.MouseLeave:Connect(function()
                    TweenService:Create(Button, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(45, 45, 45)}):Play()
                end)
                
                -- Efeito de clique
                Button.MouseButton1Down:Connect(function()
                    TweenService:Create(Button, TweenInfo.new(0.1), {BackgroundColor3 = UIConfig.AccentColor}):Play()
                end)
                
                Button.MouseButton1Up:Connect(function()
                    TweenService:Create(Button, TweenInfo.new(0.1), {BackgroundColor3 = Color3.fromRGB(55, 55, 55)}):Play()
                end)
                
                -- Callback do botão
                Button.MouseButton1Click:Connect(function()
                    if callback then
                        callback()
                    end
                end)
                
                return ButtonFrame
            end
            
            -- Função para criar toggle dentro do grupo
            function Group:AddToggle(toggleText, defaultState, callback)
                GroupElementCount = GroupElementCount + 1
                
                local ToggleFrame = Instance.new("Frame")
                local ToggleLabel = Instance.new("TextLabel")
                local ToggleButton = Instance.new("TextButton")
                local ToggleUICorner = Instance.new("UICorner")
                local ToggleIndicator = Instance.new("Frame")
                local ToggleIndicatorUICorner = Instance.new("UICorner")
                
                ToggleFrame.Name = "Toggle_" .. toggleText
                ToggleFrame.Parent = GroupContainer
                ToggleFrame.BackgroundTransparency = 1
                ToggleFrame.Size = UDim2.new(1, 0, 0, 30)
                ToggleFrame.LayoutOrder = GroupElementCount
                
                ToggleLabel.Name = "ToggleLabel"
                ToggleLabel.Parent = ToggleFrame
                ToggleLabel.BackgroundTransparency = 1
                ToggleLabel.Position = UDim2.new(0, 5, 0, 0)
                ToggleLabel.Size = UDim2.new(0.7, -10, 1, 0)
                ToggleLabel.Font = UIConfig.Font
                ToggleLabel.Text = toggleText
                ToggleLabel.TextColor3 = UIConfig.TextColor
                ToggleLabel.TextSize = 14
                ToggleLabel.TextXAlignment = Enum.TextXAlignment.Left
                
                ToggleButton.Name = "ToggleButton"
                ToggleButton.Parent = ToggleFrame
                ToggleButton.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
                ToggleButton.Position = UDim2.new(0.7, 5, 0.5, -10)
                ToggleButton.Size = UDim2.new(0.3, -5, 0, 20)
                ToggleButton.Text = ""
                ToggleButton.AutoButtonColor = false
                
                ToggleUICorner.Parent = ToggleButton
                ToggleUICorner.CornerRadius = UIConfig.CornerRadius
                
                ToggleIndicator.Name = "ToggleIndicator"
                ToggleIndicator.Parent = ToggleButton
                ToggleIndicator.AnchorPoint = Vector2.new(0, 0.5)
                ToggleIndicator.BackgroundColor3 = UIConfig.TextColor
                ToggleIndicator.Position = UDim2.new(0, 2, 0.5, 0)
                ToggleIndicator.Size = UDim2.new(0, 16, 0, 16)
                
                ToggleIndicatorUICorner.Parent = ToggleIndicator
                ToggleIndicatorUICorner.CornerRadius = UDim.new(0, 4)
                
                -- Estado do toggle
                local Enabled = defaultState or false
                
                -- Função para atualizar o estado visual
                local function UpdateToggle()
                    if Enabled then
                        TweenService:Create(ToggleIndicator, TweenInfo.new(0.2), {
                            Position = UDim2.new(1, -18, 0.5, 0),
                            BackgroundColor3 = UIConfig.AccentColor
                        }):Play()
                        TweenService:Create(ToggleButton, TweenInfo.new(0.2), {
                            BackgroundColor3 = Color3.fromRGB(60, 60, 60)
                        }):Play()
                    else
                        TweenService:Create(ToggleIndicator, TweenInfo.new(0.2), {
                            Position = UDim2.new(0, 2, 0.5, 0),
                            BackgroundColor3 = UIConfig.TextColor
                        }):Play()
                        TweenService:Create(ToggleButton, TweenInfo.new(0.2), {
                            BackgroundColor3 = Color3.fromRGB(45, 45, 45)
                        }):Play()
                    end
                end
                
                -- Configurar estado inicial
                UpdateToggle()
                
                -- Evento de clique
                ToggleButton.MouseButton1Click:Connect(function()
                    Enabled = not Enabled
                    UpdateToggle()
                    
                    if callback then
                        callback(Enabled)
                    end
                end)
                
                -- Chamada inicial do callback com o estado padrão
                if callback and defaultState then
                    callback(defaultState)
                end
                
                return ToggleFrame
            end
            
            -- Função para adicionar campo numérico ao grupo
            function Group:AddNumeric(numericText, min, max, default, callback)
                GroupElementCount = GroupElementCount + 1
                
                local NumericFrame = Instance.new("Frame")
                local NumericLabel = Instance.new("TextLabel")
                local NumericValue = Instance.new("TextBox")
                local NumericUICorner = Instance.new("UICorner")
                local NumericDecrease = Instance.new("TextButton")
                local NumericIncrease = Instance.new("TextButton")
                
                NumericFrame.Name = "Numeric_" .. numericText
                NumericFrame.Parent = GroupContainer
                NumericFrame.BackgroundTransparency = 1
                NumericFrame.Size = UDim2.new(1, 0, 0, 30)
                NumericFrame.LayoutOrder = GroupElementCount
                
                NumericLabel.Name = "NumericLabel"
                NumericLabel.Parent = NumericFrame
                NumericLabel.BackgroundTransparency = 1
                NumericLabel.Position = UDim2.new(0, 5, 0, 0)
                NumericLabel.Size = UDim2.new(0.5, -10, 1, 0)
                NumericLabel.Font = UIConfig.Font
                NumericLabel.Text = numericText
                NumericLabel.TextColor3 = UIConfig.TextColor
                NumericLabel.TextSize = 14
                NumericLabel.TextXAlignment = Enum.TextXAlignment.Left
                
                NumericValue.Name = "NumericValue"
                NumericValue.Parent = NumericFrame
                NumericValue.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
                NumericValue.Position = UDim2.new(0.5, 5, 0.5, -12)
                NumericValue.Size = UDim2.new(0.3, -10, 0, 24)
                NumericValue.Font = UIConfig.Font
                NumericValue.Text = tostring(default)
                NumericValue.TextColor3 = UIConfig.TextColor
                NumericValue.TextSize = 14
                NumericValue.ClearTextOnFocus = true
                
                NumericUICorner.Parent = NumericValue
                NumericUICorner.CornerRadius = UIConfig.CornerRadius
                
                NumericDecrease.Name = "NumericDecrease"
                NumericDecrease.Parent = NumericFrame
                NumericDecrease.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
                NumericDecrease.Position = UDim2.new(0.8, 5, 0.5, -12)
                NumericDecrease.Size = UDim2.new(0.1, -5, 0, 24)
                NumericDecrease.Font = UIConfig.Font
                NumericDecrease.Text = "-"
                NumericDecrease.TextColor3 = UIConfig.TextColor
                NumericDecrease.TextSize = 16
                NumericDecrease.AutoButtonColor = false
                
                local NumericDecreaseUICorner = Instance.new("UICorner")
                NumericDecreaseUICorner.Parent = NumericDecrease
                NumericDecreaseUICorner.CornerRadius = UIConfig.CornerRadius
                
                NumericIncrease.Name = "NumericIncrease"
                NumericIncrease.Parent = NumericFrame
                NumericIncrease.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
                NumericIncrease.Position = UDim2.new(0.9, 0, 0.5, -12)
                NumericIncrease.Size = UDim2.new(0.1, -5, 0, 24)
                NumericIncrease.Font = UIConfig.Font
                NumericIncrease.Text = "+"
                NumericIncrease.TextColor3 = UIConfig.TextColor
                NumericIncrease.TextSize = 16
                NumericIncrease.AutoButtonColor = false
                
                local NumericIncreaseUICorner = Instance.new("UICorner")
                NumericIncreaseUICorner.Parent = NumericIncrease
                NumericIncreaseUICorner.CornerRadius = UIConfig.CornerRadius
                
                -- Configurar valor
                local currentValue = default
                
                local function UpdateValue(newValue)
                    -- Validar valor
                    newValue = math.clamp(tonumber(newValue) or default, min, max)
                    currentValue = newValue
                    NumericValue.Text = tostring(newValue)
                    
                    if callback then
                        callback(newValue)
                    end
                end
                
                -- Eventos dos botões e campo
                NumericDecrease.MouseButton1Click:Connect(function()
                    UpdateValue(currentValue - 1)
                end)
                
                NumericIncrease.MouseButton1Click:Connect(function()
                    UpdateValue(currentValue + 1)
                end)
                
                NumericValue.FocusLost:Connect(function(enterPressed)
                    UpdateValue(NumericValue.Text)
                end)
                
                -- Chamada inicial do callback
                if callback then
                    callback(default)
                end
                
                return NumericFrame
            end
            
            -- Função para adicionar label ao grupo
            function Group:AddLabel(labelText)
                GroupElementCount = GroupElementCount + 1
                
                local LabelFrame = Instance.new("Frame")
                local Label = Instance.new("TextLabel")
                
                LabelFrame.Name = "Label_" .. labelText
                LabelFrame.Parent = GroupContainer
                LabelFrame.BackgroundTransparency = 1
                LabelFrame.Size = UDim2.new(1, 0, 0, 20)
                LabelFrame.LayoutOrder = GroupElementCount
                
                Label.Name = "Label"
                Label.Parent = LabelFrame
                Label.BackgroundTransparency = 1
                Label.Position = UDim2.new(0, 5, 0, 0)
                Label.Size = UDim2.new(1, -10, 1, 0)
                Label.Font = UIConfig.Font
                Label.Text = labelText
                Label.TextColor3 = UIConfig.TextColor
                Label.TextSize = 14
                Label.TextXAlignment = Enum.TextXAlignment.Left
                Label.TextWrapped = true
                
                -- Função para atualizar a label
                local LabelObj = {}
                
                function LabelObj:UpdateText(newText)
                    Label.Text = newText
                end
                
                return LabelObj
            end
            
            -- Atualizar o tamanho do grupo com base nos elementos
            local function UpdateGroupSize()
                -- Delay para permitir que os elementos sejam adicionados
                task.defer(function()
                    local totalHeight = 0
                    for _, child in ipairs(GroupContainer:GetChildren()) do
                        if child:IsA("Frame") then
                            totalHeight = totalHeight + child.Size.Y.Offset
                        end
                    end
                    
                    -- Adicionar padding
                    totalHeight = totalHeight + (GroupElementCount - 1) * 5 + 20
                    
                    -- Atualizar tamanho do grupo
                    GroupFrame.Size = UDim2.new(1, 0, 0, totalHeight)
                end)
            end
            
            -- Conectar evento de adição de filho para atualizar o tamanho
            GroupContainer.ChildAdded:Connect(UpdateGroupSize)
            
            return Group
        end
        
        -- Função para criar uma barra de progresso
        function Tab:CreateProgressBar(progressText, min, max, default)
            ElementCount = ElementCount + 1
            
            local ProgressFrame = Instance.new("Frame")
            local ProgressLabel = Instance.new("TextLabel")
            local ProgressBackground = Instance.new("Frame")
            local ProgressBackgroundUICorner = Instance.new("UICorner")
            local ProgressBar = Instance.new("Frame")
            local ProgressBarUICorner = Instance.new("UICorner")
            local ProgressValue = Instance.new("TextLabel")
            
            ProgressFrame.Name = "Progress_" .. progressText
            ProgressFrame.Parent = TabContent
            ProgressFrame.BackgroundTransparency = 1
            ProgressFrame.Size = UDim2.new(1, 0, 0, UIConfig.ElementHeight)
            ProgressFrame.LayoutOrder = ElementCount
            
            ProgressLabel.Name = "ProgressLabel"
            ProgressLabel.Parent = ProgressFrame
            ProgressLabel.BackgroundTransparency = 1
            ProgressLabel.Position = UDim2.new(0, 5, 0, 0)
            ProgressLabel.Size = UDim2.new(1, -10, 0, 20)
            ProgressLabel.Font = UIConfig.Font
            ProgressLabel.Text = progressText
            ProgressLabel.TextColor3 = UIConfig.TextColor
            ProgressLabel.TextSize = 14
            ProgressLabel.TextXAlignment = Enum.TextXAlignment.Left
            
            ProgressBackground.Name = "ProgressBackground"
            ProgressBackground.Parent = ProgressFrame
            ProgressBackground.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
            ProgressBackground.Position = UDim2.new(0, 5, 0, 25)
            ProgressBackground.Size = UDim2.new(1, -10, 0, 15)
            
            ProgressBackgroundUICorner.Parent = ProgressBackground
            ProgressBackgroundUICorner.CornerRadius = UIConfig.CornerRadius
            
            ProgressBar.Name = "ProgressBar"
            ProgressBar.Parent = ProgressBackground
            ProgressBar.BackgroundColor3 = UIConfig.AccentColor
            ProgressBar.Size = UDim2.new(0, 0, 1, 0)
            
            ProgressBarUICorner.Parent = ProgressBar
            ProgressBarUICorner.CornerRadius = UIConfig.CornerRadius
            
            ProgressValue.Name = "ProgressValue"
            ProgressValue.Parent = ProgressBackground
            ProgressValue.BackgroundTransparency = 1
            ProgressValue.Size = UDim2.new(1, 0, 1, 0)
            ProgressValue.Font = UIConfig.Font
            ProgressValue.Text = tostring(default) .. "/" .. tostring(max)
            ProgressValue.TextColor3 = UIConfig.TextColor
            ProgressValue.TextSize = 12
            
            -- Função para atualizar o progresso
            local Progress = {}
            
            function Progress:UpdateProgress(value)
                value = math.clamp(value, min, max)
                local percent = (value - min) / (max - min)
                
                TweenService:Create(ProgressBar, TweenInfo.new(0.3), {
                    Size = UDim2.new(percent, 0, 1, 0)
                }):Play()
                
                ProgressValue.Text = tostring(value) .. "/" .. tostring(max)
            end
            
            -- Configurar valor inicial
            Progress:UpdateProgress(default or min)
            
            return Progress
        end
        
        -- Função para criar área de notificação
        function Tab:CreateNotificationArea()
            ElementCount = ElementCount + 1
            
            local NotificationFrame = Instance.new("Frame")
            local NotificationContainer = Instance.new("ScrollingFrame")
            local NotificationUICorner = Instance.new("UICorner")
            local NotificationLayout = Instance.new("UIListLayout")
            
            NotificationFrame.Name = "NotificationArea"
            NotificationFrame.Parent = TabContent
            NotificationFrame.BackgroundTransparency = 1
            NotificationFrame.Size = UDim2.new(1, 0, 0, 150)
            NotificationFrame.LayoutOrder = ElementCount
            
            NotificationContainer.Name = "NotificationContainer"
            NotificationContainer.Parent = NotificationFrame
            NotificationContainer.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
            NotificationContainer.Position = UDim2.new(0, 5, 0, 5)
            NotificationContainer.Size = UDim2.new(1, -10, 1, -10)
            NotificationContainer.ScrollBarThickness = 4
            NotificationContainer.CanvasSize = UDim2.new(0, 0, 0, 0)
            NotificationContainer.AutomaticCanvasSize = Enum.AutomaticSize.Y
            NotificationContainer.ScrollingDirection = Enum.ScrollingDirection.Y
            
            NotificationUICorner.Parent = NotificationContainer
            NotificationUICorner.CornerRadius = UIConfig.CornerRadius
            
            NotificationLayout.Parent = NotificationContainer
            NotificationLayout.SortOrder = Enum.SortOrder.LayoutOrder
            NotificationLayout.Padding = UDim.new(0, 5)
            NotificationLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
            
            -- Função para adicionar notificações
            local NotificationArea = {}
            local NotificationCount = 0
            
            function NotificationArea:AddNotification(notificationText, notificationType, duration)
                NotificationCount = NotificationCount + 1
                
                local NotificationItem = Instance.new("Frame")
                local NotificationBackground = Instance.new("Frame")
                local NotificationUICorner = Instance.new("UICorner")
                local NotificationIcon = Instance.new("ImageLabel")
                local NotificationText = Instance.new("TextLabel")
                
                NotificationItem.Name = "Notification_" .. NotificationCount
                NotificationItem.Parent = NotificationContainer
                NotificationItem.BackgroundTransparency = 1
                NotificationItem.Size = UDim2.new(1, -10, 0, 40)
                NotificationItem.LayoutOrder = NotificationCount
                
                NotificationBackground.Name = "Background"
                NotificationBackground.Parent = NotificationItem
                NotificationBackground.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
                NotificationBackground.Size = UDim2.new(1, 0, 1, 0)
                
                NotificationUICorner.Parent = NotificationBackground
                NotificationUICorner.CornerRadius = UIConfig.CornerRadius
                
                -- Defina a cor com base no tipo
                local iconId = "0"
                if notificationType == "success" then
                    NotificationBackground.BackgroundColor3 = Color3.fromRGB(45, 85, 45)
                    iconId = "9073970907" -- Ícone de sucesso
                elseif notificationType == "error" then
                    NotificationBackground.BackgroundColor3 = Color3.fromRGB(85, 45, 45)
                    iconId = "9073970846" -- Ícone de erro
                elseif notificationType == "warning" then
                    NotificationBackground.BackgroundColor3 = Color3.fromRGB(85, 85, 45)
                    iconId = "9073970963" -- Ícone de aviso
                elseif notificationType == "info" then
                    NotificationBackground.BackgroundColor3 = Color3.fromRGB(45, 45, 85)
                    iconId = "9073970884" -- Ícone de informação
                end
                
                NotificationIcon.Name = "Icon"
                NotificationIcon.Parent = NotificationBackground
                NotificationIcon.BackgroundTransparency = 1
                NotificationIcon.Position = UDim2.new(0, 5, 0.5, -12)
                NotificationIcon.Size = UDim2.new(0, 24, 0, 24)
                NotificationIcon.Image = "rbxassetid://" .. iconId
                
                NotificationText.Name = "Text"
                NotificationText.Parent = NotificationBackground
                NotificationText.BackgroundTransparency = 1
                NotificationText.Position = UDim2.new(0, 34, 0, 0)
                NotificationText.Size = UDim2.new(1, -39, 1, 0)
                NotificationText.Font = UIConfig.Font
                NotificationText.Text = notificationText
                NotificationText.TextColor3 = UIConfig.TextColor
                NotificationText.TextSize = 14
                NotificationText.TextXAlignment = Enum.TextXAlignment.Left
                NotificationText.TextWrapped = true
                
                -- Animação de entrada
                NotificationItem.Size = UDim2.new(1, -10, 0, 0)
                TweenService:Create(NotificationItem, TweenInfo.new(0.3), {
                    Size = UDim2.new(1, -10, 0, 40)
                }):Play()
                
                -- Remover após a duração especificada
                if duration then
                    task.delay(duration, function()
                        -- Animação de saída
                        TweenService:Create(NotificationItem, TweenInfo.new(0.3), {
                            Size = UDim2.new(1, -10, 0, 0)
                        }):Play()
                        
                        -- Remover após a animação
                        task.delay(0.3, function()
                            NotificationItem:Destroy()
                        end)
                    end)
                end
                
                return NotificationItem
            end
            
            function NotificationArea:Clear()
                for _, child in pairs(NotificationContainer:GetChildren()) do
                    if child:IsA("Frame") then
                        child:Destroy()
                    end
                end
                NotificationCount = 0
            end
            
            return NotificationArea
        end
        
        return Tab
    end
    
    return Window
end

return Library
