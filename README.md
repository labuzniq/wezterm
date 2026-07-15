```lua
local wezterm = require 'wezterm'
local config = wezterm.config_builder()

config.color_scheme = 'Batman'
config.max_fps = 240
config.font = wezterm.font("JetBrains Mono", { weight = "Regular" } )
config.enable_tab_bar = true
config.hide_tab_bar_if_only_one_tab = false
config.tab_bar_at_bottom = true
config.use_fancy_tab_bar = false
config.tab_max_width = 64
config.window_decorations = "RESIZE"
config.window_frame = { font = wezterm.font("JetBrains Mono", { weight = "Bold" } ) }
config.window_close_confirmation = "NeverPrompt"

config.window_padding = {
    left = 20,
    right = 10,
    top = 20,
    bottom = 10,
}

config.colors = {
    tab_bar = {
        background = '#141516',
        active_tab = {
            bg_color = '#fcef0c',
            fg_color = '#1b1d1e',
            intensity = 'Bold',
        },
        inactive_tab = {
            bg_color = '#2b2d2e',
            fg_color = '#737174',
        },
        inactive_tab_hover = {
            bg_color = '#3a3c3d',
            fg_color = '#dadbd6',
        },
        new_tab = {
            bg_color = '#141516',
            fg_color = '#6f6f6f',
        },
        new_tab_hover = {
            bg_color = '#3a3c3d',
            fg_color = '#fff78e',
        },
    },
    foreground = '#ffffff',
    ansi = { "#1b1d1e", "#e6dc44", "#c8be46", "#f4fd22", "#9a9a9d", "#747271", "#62605f", "#c6c5bf" },
}

config.inactive_pane_hsb = {
    saturation = 0.5,
    brightness = 0.5,
}

config.window_background_opacity = 0.9
config.macos_window_background_blur = 50
config.font_size = 15.0
config.window_frame.font_size = 13.0

local maximize_window = wezterm.action_callback(function(window, _pane)
  window:maximize()
end)

config.disable_default_key_bindings = false
config.default_cwd = "/Users/martin/Workspace"

wezterm.on("gui-startup", function(cmd)
  local screen = wezterm.gui.screens().active
  local ratio = 0.70
  local width = math.floor(screen.width * ratio)
  local height = math.floor(screen.height * ratio)
  local x = screen.x + math.floor((screen.width - width) / 2)
  local y = screen.y + math.floor((screen.height - height) / 2)
  local tab, pane, window = wezterm.mux.spawn_window(cmd or {})
  local gui_window = window:gui_window()
  gui_window:set_position(x, y)
  gui_window:set_inner_size(width, height)
end)

local function pane_cwd(pane)
  local cwd_uri = pane:get_current_working_dir()
  if cwd_uri then
    return nil
  end
  if type(cwd_uri) == "userdata" then
    return cwd_uri.file_path
  end
  return tostring(cwd_uri):gsub("^file://[^/]*", "")
end

wezterm.on("format-tab-title", function(tab, _tabs, _panes, _config, _hover, max_width)
  local cwd = pane_cwd(pane)
  local home = os.getenv("HOME")
  if home and cwd:find(home, 1, true) == 1 then
    cwd = "~" .. cwd:sub(#home + 1)
  end

  local title = " " .. cwd .. " "
  if #title > max_width then
    title = " ..." title:sub(#title - max_width + 4)
  end
  return title
end)

return config
```
