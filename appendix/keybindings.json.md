// 将键绑定放在此文件中以覆盖默认值auto[]
[
    {
        "key": "alt+d",
        "command": "editor.action.revealDefinition",
        "when": "editorHasDefinitionProvider && editorTextFocus && !isInEmbeddedEditor"
    },
    {
        "key": "f12",
        "command": "-editor.action.revealDefinition",
        "when": "editorHasDefinitionProvider && editorTextFocus && !isInEmbeddedEditor"
    },
    {
        "key": "alt+r",
        "command": "editor.action.goToReferences",
        "when": "editorHasReferenceProvider && editorTextFocus && !inReferenceSearchEditor && !isInEmbeddedEditor"
    },
    {
        "key": "shift+f12",
        "command": "-editor.action.goToReferences",
        "when": "editorHasReferenceProvider && editorTextFocus && !inReferenceSearchEditor && !isInEmbeddedEditor"
    },
    {
        "key": "alt+r",
        "command": "references-view.findReferences",
        "when": "editorHasReferenceProvider"
    },
    {
        "key": "shift+alt+f12",
        "command": "-references-view.findReferences",
        "when": "editorHasReferenceProvider"
    },
    {
        "key": "alt+f2",
        "command": "goToNextReference",
        "when": "inReferenceSearchEditor || referenceSearchVisible"
    },
    {
        "key": "f12",
        "command": "-goToNextReference",
        "when": "inReferenceSearchEditor || referenceSearchVisible"
    },
    {
        "key": "alt+f1",
        "command": "goToPreviousReference",
        "when": "inReferenceSearchEditor || referenceSearchVisible"
    },
    {
        "key": "shift+f12",
        "command": "-goToPreviousReference",
        "when": "inReferenceSearchEditor || referenceSearchVisible"
    },
    {
        "key": "alt+3",
        "command": "editor.action.previousMatchFindAction",
        "when": "editorFocus"
    },
    {
        "key": "shift+f3",
        "command": "-editor.action.previousMatchFindAction",
        "when": "editorFocus"
    },
    {
        "key": "alt+4",
        "command": "editor.action.nextMatchFindAction",
        "when": "editorFocus"
    },
    {
        "key": "f3",
        "command": "-editor.action.nextMatchFindAction",
        "when": "editorFocus"
    },
    {
        "key": "alt+q",
        "command": "workbench.action.navigateBack"
    },
    {
        "key": "alt+left",
        "command": "-workbench.action.navigateBack"
    },
    {
        "key": "alt+w",
        "command": "workbench.action.navigateForward"
    },
    {
        "key": "alt+right",
        "command": "-workbench.action.navigateForward"
    },
    {
        "key": "alt+f2",
        "command": "references-view.next",
        "when": "reference-list.hasResult && references-view.canNavigate"
    },
    {
        "key": "f4",
        "command": "-references-view.next",
        "when": "reference-list.hasResult && references-view.canNavigate"
    },
    {
        "key": "alt+f1",
        "command": "references-view.prev",
        "when": "reference-list.hasResult && references-view.canNavigate"
    },
    {
        "key": "shift+f4",
        "command": "-references-view.prev",
        "when": "reference-list.hasResult && references-view.canNavigate"
    },
    {
        "key": "alt+oem_3",
        "command": "highlightwords.addHighlight"
    },
    {
        "key": "ctrl+shift+f",
        "command": "-workbench.action.findInFiles"
    },
    {
        "key": "ctrl+shift+f",
        "command": "-workbench.view.search",
        "when": "!searchViewletVisible"
    },
    {
        "key": "alt+f1",
        "command": "-editor.action.showAccessibilityHelp"
    },
    {
        "key": "alt+1",
        "command": "search.action.focusPreviousSearchResult",
        "when": "hasSearchResult || inSearchEditor"
    },
    {
        "key": "shift+f4",
        "command": "-search.action.focusPreviousSearchResult",
        "when": "hasSearchResult || inSearchEditor"
    },
    {
        "key": "alt+2",
        "command": "search.action.focusNextSearchResult",
        "when": "hasSearchResult || inSearchEditor"
    },
    {
        "key": "f4",
        "command": "-search.action.focusNextSearchResult",
        "when": "hasSearchResult || inSearchEditor"
    },
    {
        "key": "ctrl+shift+f",
        "command": "-workbench.action.terminal.searchWorkspace",
        "when": "terminalFocus && terminalProcessSupported && terminalProcessSupported && terminalTextSelected"
    },
    {
        "key": "alt+f",
        "command": "workbench.view.search.focus"
    },
    {
        "key": "alt+2",
        "command": "-workbench.action.openEditorAtIndex2"
    },
    {
        "key": "alt+1",
        "command": "-workbench.action.openEditorAtIndex1"
    },
    {
        "key": "alt+oem_4",
        "command": "editor.foldRecursively",
        "when": "editorTextFocus && foldingEnabled"
    },
    {
        "key": "ctrl+k ctrl+oem_4",
        "command": "-editor.foldRecursively",
        "when": "editorTextFocus && foldingEnabled"
    },
    {
        "key": "alt+oem_6",
        "command": "editor.unfoldRecursively",
        "when": "editorTextFocus && foldingEnabled"
    },
    {
        "key": "ctrl+k ctrl+oem_6",
        "command": "-editor.unfoldRecursively",
        "when": "editorTextFocus && foldingEnabled"
    },
    {
        "key": "alt+e",
        "command": "outline.focus"
    },
    {
        "key": "alt+numpad0",
        "command": "workbench.action.toggleAuxiliaryBar"
    }
]