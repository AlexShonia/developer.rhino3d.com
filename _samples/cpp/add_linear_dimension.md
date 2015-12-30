---
layout: code-sample-cpp
title: Add a Linear Dimension
author: dale@mcneel.com
platforms: ['Windows']
apis: ['C/C++']
languages: ['C/C++']
keywords: ['rhino']
categories: ['Unsorted']
origin: http://wiki.mcneel.com/developer/sdksamples/addlineardimension
description: Demonstrates how to add a linear dimension object.
order: 1
---

```cpp
// The following is a demonstration of how to interactively add a linear dimension object to Rhino.
CRhinoCommand::result CCommandTest::RunCommand( const CRhinoCommandContext& context )
{
  CRhinoLinearDimension* pDim = 0;

  CArgsRhinoDimLinear args;
  args.SetFirstPointPrompt( L"First dimension point" );
  args.SetSecondPointPrompt( L"Second dimension point" );
  args.SetDragPointPrompt( L"Dimension location" );
  args.SetIsInteractive( context.IsInteractive() ? true : false );

  CRhinoCommand::result rc = RhinoGetDimLinear( args, pDim, 0 );

  if( rc == success && pDim )
  {
    context.m_doc.AddObject( pDim, FALSE);
    context.m_doc.Redraw();
  }

  return rc;
}
```
